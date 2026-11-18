# Public Disclosure Policy

SuperMoE separates **proof of existence and results** from **proprietary implementation**.

## Safe to publish

High-level descriptions of the method, boxes-and-arrows diagrams, public benchmark methodology, approved measured results with their sample size, hardware and date, hardware compatibility notes, public roadmap items and approved acknowledgements.

## Keep private

- Source code of the panel and its coordinator.
- Prompts given to any model.
- Weights, thresholds and decision logic.
- Private schemas, queues and protocols.
- Findings about specific defects in the codebases under review.
- Paths, files and lines of reviewed private code.
- Raw run logs, internal validation logs and internal handoff records.
- Screenshots or recordings that show any of the above.
- Secrets, credentials, machine identifiers and local paths.
- Spend figures and unpublished partner information.

## Reporting progress

State the capability, its reviewed date and its status: working in development, in validation, or roadmap. A result is published only once the experiment that produces it has finished. An earlier passing run is not presented as current completion.

## Screenshot checklist

Before adding a screenshot or video:

1. Remove usernames, email addresses and personal identifiers unless intentionally public.
2. Remove local filesystem paths that reveal private structure.
3. Remove API keys, tokens, account IDs and private endpoints.
4. Remove private prompt text and routing or debug panels.
5. Verify visible documents and filenames are redistributable.
6. Check browser tabs, notifications, taskbar items and background windows.
7. Confirm partner logos or product claims are approved for public use.

## Benchmark checklist

1. Define the metric.
2. Record hardware and software versions where relevant.
3. Use repeatable parameters.
4. Run enough samples to avoid a one-off result, and state n.
5. Separate measured numbers from impressions.
6. Do not expose prompts or private datasets.
7. Say when hardware or services are sponsored or evaluated.

## Rule of thumb

> Publish what SuperMoE does, how it is measured and what the measurements show. Do not publish the implementation that creates the advantage.
