# Email triage

Classifies email by direct-to-recipient status, category, action requirement, and urgency.

## Processing flow

- Clean the email and build the state from sender, recipients, subject, date, list headers, and body.
- Group messages by Gmail `threadId`, then send one state per thread to Jev 1.13 using the newest message plus bounded recent context.
- Base direct-to-you, action, and urgency on the latest unanswered inbound message; apply one consistent classification to the thread.
- Enforce that mail written directly to the recipient is always **Personal** or **Work**; automated mail may use any category.
- Use **Email Lists** only when List-ID or a known list address proves it is a real discussion list; social-network digests are not lists. For Email Lists, deterministically extract an entity and create a nested label such as `Email Lists/gambit`. All newsletters use the single top-level `Newsletters` label; never create newsletter sublabels.
- Keep **Personal** mail in Inbox; archive other categories under their labels.
- Add `Action` only when the category is **Personal** or **Work** and action is required. Never apply it to another category.
- Add `Urgency/high` only when someone is blocked, immediate intervention is needed, or a real deadline/security risk is within 24 hours. Add `Urgency/low` when legitimate attention is needed within several days. Otherwise apply no urgency label. Spam and Potential Spam never receive urgency.
- Use **Newsletters** only for recurring author-led letters or essays clearly written by an identifiable person in a personal, conversational voice. Company-branded publications and corporate editorial content are **Potential Spam**.
- Move clearly deceptive mail—phishing, fabricated rewards, random-domain casino offers, fake dating, adult bait, and miracle cures—to **Spam**. Reserve **Potential Spam** for legitimate or plausibly legitimate unwanted bulk, platform digests, feeds, promotional content, recommendations, and re-engagement mail. Classify each message by purpose because one service may send both useful Personal alerts and promotional fluff. Generic “we miss you,” “come back,” “catch up,” and “what you missed” re-engagement messages are Potential Spam.
- Mark a message read by removing Gmail’s `UNREAD` label only when it is not in Inbox, does not have `Action`, and is not `Urgency/high`. Otherwise preserve its read state.
- Never add or remove stars.

## Classification source of truth

The runtime should read `classification.json` and pass its questions directly to Jev. Gmail labeling and routing are separate deterministic runtime behavior.

## Confidence review

- Only category confidence can send a thread to `Review`: review when the chosen category probability is below 0.70 or its top-two gap is below 0.15.
- Direct-to-you, action, and urgency confidence never block a confident category from being applied.
- Add `Action` only when the category is Personal or Work, Jev chooses yes, its probability is at least 0.70, and its top-two gap is at least 0.15. Otherwise omit `Action` without adding `Review`.
- Spam and Potential Spam have no urgency. For other categories, apply `Urgency/high` or `Urgency/low` only when Jev chooses it with probability at least 0.70 and a top-two gap of at least 0.15; otherwise apply no urgency label without adding `Review`.
- When category confidence requires review, add only `Review` and make no other automatic changes. Remove `Review` after a confident category is applied.

Synthetic fixtures are in `tests.json`.
