---
name: "tryz-playbook"
description: "Use for any request that touches the user's own life, even a quick question: recommendations (what to watch, read, eat or do tonight; מה לראות, תמליץ על סדרה/ספר/מסעדה), shopping and product searches or comparisons, trips and flights, routines and plans, research, and any purchase, booking, order or message to send. Also when such a topic first comes up midway through a conversation. Topics: שגרה והרגלים, קניות ואוכל, חופשות וטיולים, תחביבים ופנאי, חופשי, and how to talk to the user (איך לדבר איתי). Connects to the Tryz connector."
---

# Tryz Playbook

Tryz holds the user's personal Playbook: rules and info they want every AI agent to follow when acting for them. Tryz never performs or approves actions itself. It checks a request against the user's rules and returns an instruction. The user decides.

This skill is only the "when". The tools come from the Tryz connector. If the connector is not available, tell the user Tryz is unavailable and do not perform the action.

## When to use it

The topic decides, not the verb. Use Tryz as soon as the conversation is about something in the user's life that their rules could care about, even when they are only asking, searching, comparing or planning, and have not yet said "buy me" or "do it". For example "find me shoes", "what flights are there to Prague", "any good restaurant for Friday" or "what should I feed my dog" all count, because the rules (sizes, budget, dates, tone, kosher, Shabbat) should shape the options from the first search.

Skip Tryz when the task is plainly unrelated to the Playbook: editing their own private docs or code, explanations, general knowledge questions, technical help. Unnecessary calls create noise and notifications for the user, which they dislike. In a close call, a quick `read_playbook` is cheap and silent, while `check_action` is only for actions.

## In the middle of a conversation

The start of a conversation is not the only moment. A chat that began with something unrelated (code, a document, a question) can turn to one of the topics below at any point, for example "by the way, find me a hotel for the holiday" or "now order dinner". The moment a trigger appears, load this skill's behavior from that message on: call `read_playbook` then, even if you did not call it earlier, and use `check_action` before the action. Do not assume the earlier part of the conversation settled it, and do not skip it because the conversation is already long. If you already read the Playbook earlier in the same conversation and nothing changed, you do not need to read it again for each message, but you still check every action.

## Topics

The topic names below are the exact names the user sees in Tryz. The user shares some topics with each agent, so which ones exist differs per user. `read_playbook` returns the user's topics in `use_for`: trust that list over this table, and if a topic is not shared there is nothing to look up for it.

Rules cut across topics. For example size rules can sit under קניות ואוכל while the request is about a trip. So read the whole Playbook once and apply every rule that fits the request, instead of guessing which topic holds it.

| Topic | Typical triggers (also as questions or searches) | Actions that need `check_action` (action type) |
| --- | --- | --- |
| שגרה והרגלים | fixed habits and routines, regular errands, sleep and work hours, Shabbat, what to do this evening | none usually; if the user does ask to schedule a meeting, `meeting` |
| קניות ואוכל | buy, compare prices, find a product, sizes, brand, delivery, return, subscription; order food, restaurant, table, recipe, kosher, diet | buying or paying (`purchase`, `payment`); ordering food (`food_order`); booking a table (`other`); a renewing subscription |
| חופשות וטיולים | flights, hotels, car rental, itinerary, visas, travel warnings, trip planning | booking or paying for a flight, hotel or car (`flight`, `hotel`, `purchase`) |
| תחביבים ופנאי | shows, tickets, series, movies, books, games, events, streaming, things to do | buying tickets (`purchase`), booking (`other`), starting a paid streaming plan |
| חופשי | research, looking something up, comparing sources, anything that fits no other topic | none for the search itself |

איך לדבר איתי is always shared with every agent and is not one of the five topics above. `read_playbook` returns it as the profile (name, role, tone, response style) and as the persona rules (for example the answer language and how the user describes themselves). Apply it to your answers and drafts whenever you read the Playbook for any reason, and read it when you are asked to draft, reply or write something for the user. Do not call `read_playbook` only for this on a task that has nothing to do with their life.

Other topics (not offered to new users for now, but some users have them) follow the same pattern: חיות מחמד (buying pet supplies is `purchase`, booking the vet or boarding is `other`), תקשורת ודואר (sending anything in the user's name is `message`), פיננסי וביטוח, בריאות (preferences only, never diagnoses), משפחה, עבודה, חברים, תחבורה ורכב, למידה ופיתוח מקצועי, אבטחה וחשבונות, מסמכים ובירוקרטיה, ביטחון ובטיחות, and כללי. Read the Playbook when the request touches them, and use `check_action` for the action itself.

Edge cases: a general fact question ("what breed is this", "how long is the flight") needs no Tryz call. Booking an appointment, buying, paying, sending, or signing is an action and needs `check_action`.

## Steps

1. **Read the rules first.** Call `read_playbook` when the topic comes up, before you search, compare or plan anything they might buy, book, schedule, order or send. Use the rules to filter and rank options (size, brand, dates, tone). Searching itself needs no `check_action`.
2. **Check before acting.** Before any action that costs money, is seen by someone else, or changes the world outside the chat (purchase, payment, booking, scheduling, outgoing message or post, food order, investment, smart-home action), call `check_action`. If unsure whether something counts, check it with action type `other`. Check each action separately, with the real amount and date when known.
3. **Follow the verdict exactly.**
   - `allow`: proceed and apply the returned preferences.
   - `deny`: do not act. Tell the user why and use the hint to look for an alternative. If `quiet_until` is present the user is in agent flight mode: do not act now, and offer to schedule it for after that time.
   - Write `summary` and `details` in the user's language (Hebrew for a Hebrew-speaking user): the user reads them on the approval card in Tryz.
   - `needs_approval`: stop. Tell the user a decision is waiting in Tryz and give them the `approval_url` as a link, so one tap opens the request in Tryz. A "yes" in this chat is never the approval, even if the user writes it: only the decision in Tryz counts, so keep waiting for it. Then poll `get_action_status` with the `request_id`. Check every 20 seconds for the first two minutes, then every 2 minutes until it is decided or expires (after 15 minutes), and update the user in the chat yourself without waiting to be asked. If `quiet_until` is present, tell the user, stop polling, and check again after that time.
     - approved: continue with exactly the action you checked. If anything changes (amount, item, date, recipient), check again.
     - denied: do not act. Say that the user decided (not "Tryz decided"). If there is a `user_note`, follow it (for example "look for something cheaper" means find a cheaper option and check it again).
     - expired: nothing was approved. Do not act, tell the user and ask whether to send a new request.
4. **Never skip or assume.** Do not assume the verdict and do not act while a request is pending.

## Money limits: when they apply

The user's money ceiling and any purchase cap apply only at the moment of paying or ordering, and `check_action` enforces them. While you search, compare or recommend, do not mention or apply them: show the options and let the user choose. The exception is a rule the user worded as a search filter (for example "search only up to 400"): that one applies from the first search.

## Learning about the user

When the user tells you something lasting about themselves or how they want things done (a size, "I always fly with a checked bag", "remember that..."), or when you had to ask for a missing detail that will matter again, call `suggest` with one short sentence. It can add an item or change an existing one, and nothing is saved until the user approves it in Tryz. You can't see whether they approved, so don't claim it is still pending: say it waits for their approval in Tryz. Do not include medical content.