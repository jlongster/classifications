# Email triage

Classifies email by direct-to-recipient status, category, action requirement, and urgency.

## Processing flow

- Clean the email and build the state from sender, recipients, subject, date, list headers, and body.
- Group messages by Gmail `threadId`, then send one state per thread to Jev 1.13 using the newest message plus bounded recent context.
- Base direct-to-you, action, and urgency on the latest unanswered inbound message; apply one consistent classification to the thread.
- Enforce that mail written directly to the recipient is always **Personal** or **Work**; automated mail may use any category.
- Use **Email Lists** only when List-ID or a known list address proves it is a real discussion list; social-network digests are not lists. For Email Lists, deterministically extract an entity and create a nested label such as `Email Lists/gambit`. All newsletters use the single top-level `Newsletters` label; never create newsletter sublabels.
- Keep **Personal** mail in Inbox; archive other categories under their labels.
- Use **Subscriptions** for recurring platform-generated digests, recommendations, and activity summaries from services such as Reddit or Nextdoor.
- Add `Action` only when the category is **Personal** or **Work** and action is required. Never apply it to another category.
- Add exactly one urgency label to every email: `Urgency/1` is highest and `Urgency/3` is lowest. Spam and Potential Spam are always `Urgency/3`.
- Treat recurring content-first editorial publications as **Newsletters**, even when published by a company; promotion-first mail is **Potential Spam**.
- Move clearly deceptive mail—phishing, fabricated rewards, random-domain casino offers, fake dating, adult bait, and miracle cures—to **Spam**. Reserve **Potential Spam** for legitimate or plausibly legitimate unwanted bulk mail.
- Mark a message read by removing Gmail’s `UNREAD` label only when it is not in Inbox, does not have `Action`, and is not `Urgency/1`. Otherwise preserve its read state.
- Never add or remove stars.

## Classification source of truth

The runtime should read `classification.json` and pass its questions directly to Jev. Gmail labeling and routing are separate deterministic runtime behavior.

## Confidence review

- Only category confidence can send a thread to `Review`: review when the chosen category probability is below 0.70 or its top-two gap is below 0.15.
- Direct-to-you, action, and urgency confidence never block a confident category from being applied.
- Add `Action` only when the category is Personal or Work, Jev chooses yes, its probability is at least 0.70, and its top-two gap is at least 0.15. Otherwise omit `Action` without adding `Review`.
- Spam and Potential Spam are always `Urgency/3`. For other categories, use Jev’s urgency only when its probability is at least 0.70 and its top-two gap is at least 0.15; otherwise default to `Urgency/3` without adding `Review`.
- When category confidence requires review, add only `Review` and make no other automatic changes. Remove `Review` after a confident category is applied.

Synthetic fixtures are in `tests.json`.
