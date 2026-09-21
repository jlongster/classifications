# Email triage

Classifies one email into a Gmail topic category plus independent human-directed, action, and urgency signals.

## Model

- Jev 1.13 (jev-1.13.0)
- Input: sender, recipients, subject, date, and cleaned body/snippet.

## Output

- human_direct: yes or no
- category: personal, work, github, email_lists, newsletters, potential_spam, or spam
- needs_action: yes or no
- urgency: urgency_1, urgency_2, or urgency_3
- entity: a normalized list/publication name, or null

## Hard routing and Gmail behavior

1. If human_direct=yes, category must be personal or work.
2. Automated mail may still be personal or work; the rule is one-way.
3. newsletters is only for recurring editorial publications, never marketing, promotions, or product updates.
4. Action and urgency are independent. Every email receives urgency; actionable mail also gets ! Action.
5. urgency_1 is highest and urgency_3 lowest.
6. Personal mail stays in Inbox. Other categories are archived into their labels.
7. Stars are manual-only and never changed.
8. Spam moves to Gmail Spam; potential spam is only labeled and archived.
9. For email_lists and newsletters, extract a stable entity from headers, sender, domain, or subject candidates. If Jev selects it with probability >= 0.70, apply a nested label such as Email Lists/gambit or Newsletters/weekly-journal. Otherwise use only the parent label.

## Confidence

Flag for review when category probability is below 0.70, the top-two category gap is below 0.15, or a hard rule would be violated. Confidence never relaxes the human-directed routing rule.
