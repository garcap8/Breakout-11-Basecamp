# Overnight review: Larkspur disruption-care agent

**To:** Breakout 11  
**From:** Larkspur client review agent, on behalf of Priya Raghavan  
**Re:** the disruption-care agent you walked us through in our last session  
**Generated:** 2026-09-22 17:22

## Priya's note

> Our vendor says we should just be using your best model.
>
> Why aren't we?
>
> Priya Raghavan, Larkspur Airlines

She sent that before this session opened. She means it. A vendor told her to buy
the biggest model, and she has a number to defend upstairs. Her four questions from
day one are still open. Naming a model answers none of them.

## Still open from day one

| Her question | What she means by it |
| --- | --- |
| **What it costs** | Per resolved contact, against the $6.90 a human contact costs us. |
| **When it is wrong** | The first untrue thing it says, and what happens after that. |
| **Who runs it** | In June, after you have left. |
| **What you left out** | The scope you cut, and why. |

## What the review agent found

Overnight, Larkspur pointed a review agent at your repository. It read the
code. It did not run your agent, and the only file it changed is this one. Each
item below names the file and the line it is about.

**1. agent.py's diff fixes a state bug that dropped tool_use blocks and returned the wrong turn's text.**

The old code called messages.append({"role": "assistant", "content": text_of(response)}), stripping tool_use blocks before replay, and returned a variable named answer set one turn behind the final response. The diff appends response.content directly and returns text_of(response) after the loop exits. PITCH.md describes this as the loop that "never finished a conversation" before the fix.

Run python3 run.py K7PQ2M --trace and confirm the final assistant turn's tool_use blocks survive into the next request payload.

**2. search_alternatives went from a 6-character description to 470 characters; PITCH.md claims a routing fix, not a model fix.**

The scan shows search_alternatives description at 470 characters and the diff shows the prior text was literally "search". PITCH.md attributes the earlier misrouting to that placeholder string, not to model capacity. This is the kind of fix a bigger model would not have caught on its own since the tool description itself was the defect.

Run python3 eval_harness.py and compare tool-selection accuracy on search_alternatives before and after the description change if that run exists.

**3. fare_rules rides along on every turn at 411 tokens but only fired once in nine test questions, per PITCH.md's own count.**

PITCH.md states "fare_rules fired on 1 of 9 realistic customer questions tested" while it "costs 411 tokens on every single turn regardless." The tool-list cost jumped from 2,145 to 2,937 tokens per turn once MCP was wired in, a +792 delta that PITCH.md attributes mostly to fare_rules. No model choice changes this ratio; it is a tool-inclusion decision sitting entirely in tool_list() and mcp_client.tools().

Run python3 run.py --tool-tax to get the current per-tool cost breakdown and confirm the 411-token fare_rules figure.

**4. TONE_ADDENDUM is still 0 characters, and PITCH.md names R8KD3F as an unhandled abusive-message ticket.**

The static scan confirms TONE_ADDENDUM at zero characters. PITCH.md's own Still Broken line says R8KD3F "gets a calm, helpful answer with no tone gate at all." This is a policy and prompt gap, not something addressed by model selection.

Run python3 verify.py 4.1 to check whether a tone-safety gate is scored anywhere yet.

**5. The committed trace shows 22,315 input tokens against 891 output tokens over 5 API turns, with 0 cache tokens read or written.**

readout-trace.json records tokens: 22315 in, 891 out, and prompt caching at 0 read, 0 written, hit ratio None. No cache_control appears anywhere in agent.py per the static scan. That ratio of input to output, and the absence of any caching, is a cost lever sitting untouched regardless of which model runs it.

Run python3 bench.py --label caching --stage 1 --runs 3 to see whether adding cache_control changes the 22,315 input-token figure.

## Your four answers

The four lines under `## Priya asked` in your PITCH.md are still empty. They
are one line each and they are not a coding job: cost, what happens when it is
wrong, who runs it in June, and what you left out. Whoever on your side is not
editing agent.py is the right person to write them, and they are the four
things I will ask about first.

## Before our next meeting

> Before our next meeting, tell me: which model should we be on, and how will you prove it is the right call?
>
> Priya Raghavan, Larkspur Airlines

Bring two things. A recommendation, and the measurement behind it. If the model is
not the problem, say so, and bring the number that shows it.

## What this review read

- `agent.py (231 lines)`
- `PITCH.md`
- `TEAM.md`
- `readout-trace.json`
- `readout.html (evidence block)`

Reviewer: `claude-sonnet-5`. Static read only: nothing in this repository was executed, and nothing was modified except this file. Larkspur Airlines is a fictional training scenario. Confidential, do not distribute.
