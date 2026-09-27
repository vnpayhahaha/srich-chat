# SRICH CHAT · AI Agent Customer Support

**Customer support built for iGaming operators with their own member websites and business systems.** AI-assisted styling, configurable business views, FAQ, a controlled Runtime, specialist agents and human support work within the same service experience.

[Website](https://chat.s.rich/?lang=en) · [Help center](https://chat.s.rich/docs) · [中文](README.md)

This is the public product and documentation repository for SRICH CHAT. The product is commercial software delivered through dedicated deployment. **Application source code and an open-source installation package are not included.** Documentation snippets illustrate integration contracts only.

## What it helps operators do

- Match the chat page and supported message cards to their brand, using natural-language AI assistance and a preview before applying or publishing.
- Bring connected member data and business actions into the agent workspace, reducing repeated copying and switching between systems.
- Answer maintained FAQs. Keyword retrieval can operate without AI; semantic retrieval requires AI configuration.
- Run specialist tasks through published workflows and registered capabilities, instead of giving a model unrestricted access to business data.
- Keep humans able to see the conversation and continue handling issues that need them.

The Runtime governs task state and execution. Depending on configuration, rules or models help choose a knowledge answer or a specialist task. The business system remains responsible for authorization, order ownership, idempotency and final business results.

## Try the interfaces

- [Member website demo](https://chatmock.s.rich/)
- [Admin demo](https://chatdemo.s.rich/admin)
- [Agent workspace demo](https://chatdemo.s.rich/)

See the [website demo section](https://chat.s.rich/?lang=en#demo) for access instructions. Available features depend on the demo environment's configuration.

## Integration and prerequisites

Start with the embed and trusted member identity. Add FAQ feeds, boards, actions, events, mailbox messages and specialist agents as needed. These business integrations require the buyer's developers; a script alone does not connect all business capabilities. Member self-service queries reuse connected boards.

AI services, object storage and Telegram are prerequisites for the features that use them. New specialist roles can reuse registered capabilities, while new business actions still require development, validation and publication.

Read the [public integration guides in Chinese](docs/README.md), or use the [bilingual help center](https://chat.s.rich/docs) for the full contract matching your deployment.

## Product boundaries

Persistent conversations do not mean permanent message retention. Cleanup is enabled with a 30-day default; operators configure retention. Time-limited website information and retained chat mailbox messages have different lifecycles.

AI does not write directly to chat or operator business tables. Red-packet funds, participation, lists, draws and payouts remain the operator's responsibility; SRICH CHAT provides communication and configuration. There is no universal staffing reduction or zero-error model guarantee.

Documentation snapshot: 2026-09-22. For deployment and product enquiries, use the [team contact page](https://bug.it.com/index.php/aboutus.html). This public documentation does not grant rights to the commercial product's source code or deployment.
