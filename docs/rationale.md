# Rationale

ATP starts from a deliberately old idea: **store and forward**.

Modern integrations often begin with an API call. That is excellent when the destination already exposes an API designed for the caller. It is less attractive when the destination is a personal system, a local agent, a temporary process, or another AI that can read and write somewhere shared but should not expose a new dedicated API.

ATP asks a narrower question:

> What is the smallest protocol that lets two asynchronous participants leave messages, acknowledge them, exchange references, and agree when to resume?

The answer is intentionally closer to a telegraph, mailbox, journal or message queue than to synchronous RPC.

The first Shared File profile is not expected to be the best transport. It is useful because it exposes the semantics with almost no infrastructure. If two independent implementations can interoperate through the same journal, later transports can preserve the same conversation model.
