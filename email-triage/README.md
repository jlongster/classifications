# Email triage

Classifies email by direct-to-recipient status, category, action requirement, and urgency.

## Processing flow

- Clean the email and build the state from sender, recipients, subject, date, list headers, and body.
- Group messages by Gmail `threadId`, then send one state per thread to Jev 1.13 using the newest message plus bounded recent context.
- Base direct-to-you, action, and urgency on the latest unanswered inbound message; apply one consistent classification to the thread.
- Enforce that mail written directly to the recipient is always **Personal** or **Work**; automated mail may use any category.
- Use **Email Lists** only when List-ID or a known list address proves it is a real discussion list; social-network digests are not lists. For Email Lists and Newsletters, deterministically extract an entity and create a nested label such as `Email Lists/gambit`.
- Keep **Personal** mail in Inbox; archive other categories under their labels.
- Add `! Action` only when the category is **Personal** or **Work** and action is required. Never apply it to another category.
- Add exactly one urgency label to every email: `Urgency: 1` is highest and `Urgency: 3` is lowest. Spam and Potential Spam are always `Urgency: 3`.
- Treat recurring content-first editorial publications as **Newsletters**, even when published by a company; promotion-first mail is **Potential Spam**.
- Move clearly deceptive mail—phishing, fabricated rewards, random-domain casino offers, fake dating, adult bait, and miracle cures—to **Spam**. Reserve **Potential Spam** for legitimate or plausibly legitimate unwanted bulk mail.
- Mark a message read by removing Gmail’s `UNREAD` label only when it is not in Inbox, does not have `! Action`, and is not `Urgency: 1`. Otherwise preserve its read state.
- Never add or remove stars.

## Classification source of truth

The runtime should read `classification.json` and pass its questions directly to Jev. Gmail labeling and routing are separate deterministic runtime behavior.

## Confidence review

- Review a result when the selected probability is below 0.70 or the top-two gap is below 0.15.
- Check category and direct-to-you for every thread.
- Check action only for Personal and Work; other categories are hard-set to no action.
- Check urgency except for Spam and Potential Spam, which are hard-set to Urgency 3.
- For a thread requiring review, add `! Review` and make no other automatic changes: do not apply category, action, urgency, archive, Inbox, or Spam operations. Remove `! Review` after the thread is resolved or successfully reclassified.

Synthetic fixtures are in `tests.json`.
