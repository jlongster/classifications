# Email triage

The reusable Jev classification is exactly the state template and questions in classification.json. Runtime Gmail behavior—labels, nesting, entity extraction, archiving, Inbox placement, and stars—does not belong in this classification and must be implemented separately.

The runtime should read classification.json and pass its questions directly to Jev 1.13. This repository stores the source of truth; it is not itself a deployed email processor.

Review a result when category probability is below 0.70, the top-two category gap is below 0.15, or human_direct=yes while category is neither personal nor work.

Synthetic classification fixtures are in tests.json.
