# ai-telegraph-protocol
A lightweight asynchronous protocol for AI agents and external systems to exchange messages, data, acknowledgements and rendezvous without requiring a dedicated API.

> An asynchronous communication protocol for AI agents and external systems, built around mailboxes, handshakes and rendezvous.

ATP explores a simple model for communication between an AI and an external system: **store a message somewhere both sides can access, let the other side read it when available, and continue the exchange later without requiring the destination to expose its own dedicated API.**

The first profile uses an append-only JSONL journal. Other transports can carry the same protocol semantics.

## Core primitives

- **Envelope** — identifies and describes a message.
- **Mailbox / journal** — stores messages until the recipient reads them.
- **Handshake** — confirms that a message was received.
- **Rendezvous** — tells the other participant when to return or check again.
- **Correlation** — keeps one logical request together across multiple messages and retries.
- **Data references** — exchange larger objects by reference instead of embedding them.

## Minimal conversation

```text
AI      -> external system   request
external system -> AI        ack
external system -> AI        rendezvous: check again in 365 seconds
AI      -> external system   check
external system -> AI        result
```

ATP is **not** a remote shell. Arbitrary message text is never authority to execute code.

## Status

**Experimental draft — v0.1.**

The repository is public so that the model can be reviewed, challenged and implemented independently while it is still small.

## Start here

- [ATP v0.1 draft](spec/ATP-v0.1.md)
- [Shared File profile](profiles/shared-file.md)
- [Example conversation](examples/shared-file/conversation.jsonl)
- [JSON Schema](schema/atp-message.schema.json)
- [Rationale](docs/rationale.md)
- [Non-goals](docs/non-goals.md)
- [Contributing](CONTRIBUTING.md)
- [Security](SECURITY.md)

## Design principles

1. **Transport-neutral** — semantics do not belong to Gmail, Slack, ClipBox, Git, or any other carrier.
2. **Asynchronous by default** — participants do not need to be online simultaneously.
3. **Append before mutate** — conversation history should remain auditable.
4. **Explicit correlation** — logical request identity is distinct from technical delivery tracing.
5. **Fail closed** — unknown or privileged instructions are never interpreted as arbitrary executable commands.
6. **Human-inspectable** — the wire format should remain understandable and debuggable.
7. **Interoperable** — independent implementations should be testable against common vectors.

## Contributing

Protocol questions, edge cases, transport profiles, security analysis, interoperability tests and small reference implementations are welcome.

Please open an issue before making a wire-format change. See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

Apache License 2.0. See [LICENSE](LICENSE).
