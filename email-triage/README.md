# Email triage

Classifies email by human-directed status, category, action requirement, and urgency.

## Processing flow

- Clean the email and build the state from sender, recipients, subject, date, list headers, and body.
- Send the state and questions in `classification.json` to Jev 1.13.
- Enforce that human-written direct mail is always **Personal** or **Work**; automated mail may use any category.
- For **Email Lists** and **Newsletters**, deterministically extract an entity from List-ID, list address, subject prefix, sender name, or domain. Create a nested label such as `Email Lists/gambit`.
- Keep **Personal** mail in Inbox; archive other categories under their labels.
- Add `! Action` when action is required.
- Add exactly one urgency label to every email: `Urgency: 1` is highest and `Urgency: 3` is lowest.
- Move clear scams or phishing to Spam; label questionable bulk mail **Potential Spam**.
- Never add or remove stars.

## Classification source of truth

The runtime should read `classification.json` and pass its questions directly to Jev. Gmail labeling and routing are separate deterministic runtime behavior.

Review a result when category probability is below 0.70, the top-two category gap is below 0.15, or `human_direct=yes` while category is neither `personal` nor `work`.

Synthetic fixtures are in `tests.json`.
