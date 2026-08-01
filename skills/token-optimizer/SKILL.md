---
name: token-cost-optimization
description: Cost-and-token discipline for LLM API usage (DeepSeek via Open WebUI, and any OpenAI-compatible endpoint) when working in Positron on bioinformatics/proteomics code. Use this skill whenever generating prompts, scripts, or multi-step workflows that will call an LLM API — especially for routine code review, docstrings, refactors, MaxQuant/proteomics data wrangling, or any repeated/batch task. Also trigger when the user mentions "cost", "token budget", "cache hit", "peak pricing", "which model should I use", or asks to optimize/reduce API spend. Applies both to picking the right model tier and to shaping prompts/context so they're cheap to run.
---

# Token Cost Optimization

A practical ruleset for keeping LLM API bills low without sacrificing output quality, built from an actual DeepSeek usage audit (see `references/usage-audit.md` for the numbers this is based on).

The core fact this skill is built around: **input tokens that hit cache cost roughly 10-20x less than tokens that miss cache**, and **output tokens are always the most expensive per-token unit on every provider**. Nearly every rule below is downstream of those two facts.

## 1. Model routing — pick the cheapest tier that can do the job

Don't default to the strongest/most expensive model out of habit. Match task to tier:

| Task type | Use | Why |
|---|---|---|
| Syntax questions, docstrings, boilerplate, simple refactors, formatting fixes | **Budget/flash tier** | No real reasoning needed; cache-friendly, low output |
| Routine code review, linting logic, small bug hunts | **Budget/flash tier** | Pattern-matching task, not deep reasoning |
| Statistical/biological interpretation (DE results, pathway calls, experimental design) | **Pro/flagship tier** | Needs real reasoning; errors here are costly in a different way (wrong conclusions) |
| One-off exploratory "what if" questions on a small snippet | **Budget/flash tier** | Low stakes, cheap to be wrong and retry |
| Anything producing a final report, figure, or decision going to a PI/collaborator | **Pro/flagship tier** | Quality matters more than the marginal token cost here |

Rule of thumb: if you'd trust a careful RA/postdoc-in-training's first pass on it, use flash. If you need a senior collaborator's judgment, use pro.

## 2. Maximize cache hits — the single biggest lever

Cache-hit input tokens are dramatically cheaper than cache-miss tokens. Structure every prompt so the cache can actually work:

- **Keep stable content first, variable content last.** System prompts, SKILL.md content, coding conventions, and repo context should always appear in the same order at the start of the context. The actual question/data goes at the end.
- **Never reorder or reword unchanged context between calls.** Even trivial rewording (whitespace, punctuation) can invalidate the cache prefix on some providers.
- **Reuse the same conversation/session for a string of related requests** rather than starting fresh each time — a fresh session means a fresh cache-miss on everything.
- **Don't paste large data blobs that change every call into the front of the prompt.** If you're pasting a MaxQuant table or a big CSV, put it at the end, after anything stable.

## 3. Trim what you send — cache-miss avoidance is separate from cache-hit optimization

Even on a full cache miss, less input is less cost:

- **Filter before you paste.** Don't dump a full MaxQuant proteinGroups.txt into a prompt — filter to the relevant rows/columns first (the ones actually under discussion) and summarize row counts instead of pasting everything.
- **Summarize long file/function context instead of pasting whole files** when only one function or section is relevant. Reference "see `analysis.py` lines 40-85" in your own head, paste only that range.
- **Strip debug output, stack traces, and repeated boilerplate** before pasting error logs — keep the actually informative lines.
- **Avoid re-pasting the same context across multiple back-to-back messages** in one session; the model already has it.

## 4. Batch instead of drip-feeding

Many small requests cost more in aggregate than fewer, well-specified large ones, because each new request re-sends non-cached context and incurs its own overhead:

- Before sending a request, ask: "can I fold in the next 2-3 things I'm about to ask separately?"
- For iterative coding, write a more complete spec/ask up front (e.g., "refactor this function AND add type hints AND add a docstring") rather than three separate round-trips.
- For batch data-processing jobs (PCA reruns, volcano plot regeneration, QC sweeps across multiple files), script the loop and make one well-structured request per distinct task rather than one request per file if the files are structurally similar.

## 5. Output-length discipline

Output tokens are the most expensive line item per-token on every provider. Ask for only what you need:

- Request "just the diff" or "just the changed function" instead of "rewrite the whole file" when only part needs to change.
- Explicitly cap explanation length when you just need code ("code only, no explanation" or "one-line summary after the code").
- For exploratory brainstorming, ask for a short list first, then ask to expand only the promising items — don't ask for exhaustive detail on every option up front.

## 6. Time-of-use awareness (peak-pricing windows)

If your provider has time-based pricing (e.g., a peak-hour multiplier):

- **Defer non-urgent/batch work to off-peak hours.** Don't just "start later" — check the actual window boundaries, since starting your PC an hour later doesn't help if the peak window extends further than you think.
- **Automate the deferral, don't rely on remembering it.** Use `cron` or a scheduled task to queue batch jobs (PCA reruns, bulk QC, report regeneration) to fire automatically right after the peak window ends, rather than trusting yourself to remember every morning.
- **Reserve the peak window for compute-only work that doesn't touch the API at all** — local pipeline steps, reading, manual QC review — and save API-dependent tasks for off-peak.

## 7. Generic cost-optimization checklist (provider-agnostic)

Quick pre-flight checklist before sending any non-trivial request:

- [ ] Is this the cheapest model tier that can actually do this correctly?
- [ ] Is stable context (system prompt, skill content, conventions) placed first and unchanged from last call?
- [ ] Have I trimmed/filtered any large data before pasting it in?
- [ ] Am I asking for only the output I actually need (no unnecessary re-generation of unchanged code/text)?
- [ ] Could this be batched with 1-2 other things I'm about to ask separately?
- [ ] If this is deferrable, should it run outside a peak-pricing window?
- [ ] Am I about to paste something I've already pasted earlier in this same session?

## 8. Monitoring

- Periodically pull your provider's usage/cost export (daily granularity is usually enough) and check the cache-hit ratio and flash-vs-pro split — a dropping cache-hit ratio is often the earliest sign that prompt structure has drifted (e.g., a system prompt got edited, breaking the cached prefix).
- Watch for creeping use of the expensive/pro tier for tasks that used to go to flash — model routing habits drift over time without an explicit rule to check against (see section 1).

## Reference

See `references/usage-audit.md` for the worked example (DeepSeek v4-flash/pro, July 2026) this skill's ratios and rules of thumb are based on, including the cache-hit-vs-miss cost math and a cross-provider pricing comparison.
