# PITCH.md

Six lines and a lever. Your words. The last two are scored.

Built: Fixed the agent loop Larkspur gave us (it dropped the tool_use block when replaying history, and returned text from the wrong turn, so it never finished a conversation) and rewrote two tool descriptions Claude was routing on incorrectly (search_alternatives had no real description; get_flight_status's date field told Claude the wrong format). Connected it to all nine given tools, then added next_available_day as our own tool and moved it behind the MCP server. Both tools the MCP server carries are proven, not just routed: next_available_day fires on a date question, fare_rules fires on a policy-challenge question, each unprompted in its own trace. Resolved all 5 Stage 1 ticket types and measured each one individually: cancellation, delay, missed connection, out of scope, and abusive message.
Does: For a stranded customer, it looks up the booking, checks the live flight status, resolves what Larkspur owes under policy, and presents specific rebooking options with a "which one would you like me to hold?" prompt -- then hands off to a human for anything out of scope (groups, partners, minors), instead of guessing. It cannot finalize a booking on its own: confirm_rebooking requires a confirmation_token only the customer's own click can produce. Provable now: 5/5 ticket types resolved, with the token cost measured on both sides of the tool list.
Number: Tool-list cost per turn, counted on the wire, not sampled: 2,145 tokens with the given nine tools -> 2,937 tokens with next_available_day added and served over MCP (+792/turn; 411 of that is fare_rules, which came along for free once we pointed at the server, not something we asked for). Baseline conversation cost, Build 1: 19 API turns and 62,476 input tokens to resolve all 5 Stage 1 ticket types (avg 12,495 tokens in per ticket).
Safety check: No invented flight facts -- every claim in the final answer traces to a tool result on the wire, in order (lookup_booking -> get_flight_status -> check_policy), confirmed in the K7PQ2M trace.
Next: Build the tone-safety gate for the abusive-message case (R8KD3F) before Build 4.
Still broken: R8KD3F (the abusive-message ticket) gets a calm, helpful answer with no tone gate at all -- that's Build 4's work, not fixed here.
Lever: <cost | speed | intelligence>

## Priya asked

Costs:
Wrong:
Runs it:
Left out:
