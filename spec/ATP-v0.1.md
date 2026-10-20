# AI Telegraph Protocol (ATP) v0.1

**Status:** experimental draft

## 1. Purpose

ATP is a minimal asynchronous protocol for exchanging messages and data between an AI agent and an external system without requiring the destination to expose a dedicated API.

ATP separates **protocol semantics** from **transport**. A shared file, mailbox, messaging service, or other carrier may transport the same logical conversation.

The first profile is a shared append-only JSONL journal.

## 2. Core model

A conversation consists of ordered records. Participants append records; normal protocol operation does not rewrite previous messages.

A typical exchange is:

```text
request -> ack -> progress | question | rendezvous -> result
```

The protocol supports delayed continuation. A receiver that cannot answer immediately can append a `rendezvous` telling the other participant when to check again.

## 3. Identifiers

### `session_id`
Identifies one protocol conversation.

### `message_id`
Uniquely identifies one message record.

### `request_id`
Identifies the logical work across multiple messages, retries and rendezvous.

### `trace_id`
Optionally identifies one technical delivery/traversal for diagnostics. It is deliberately distinct from `request_id`.

## 4. Message ordering

Every non-meta message has a positive integer `seq`.

Within one session:

- `seq` MUST be strictly increasing;
- a normal append MUST use the previous sequence plus one;
- implementations MUST NOT silently overwrite a newer record;
- transports SHOULD provide stale-write detection or equivalent optimistic concurrency.

## 5. Required fields

Every non-meta ATP v0.1 message MUST contain:

- `protocol`: `"ATP"`
- `version`: `"0.1"`
- `type`
- `seq`
- `message_id`
- `session_id`
- `from`
- `to`
- `created_at`
- `next_actor`

Recommended when applicable:

- `request_id`
- `trace_id`
- `reply_to_seq`
- `status`
- `body`
- `data_refs`
- `retry_after_s`
- `next_check_at`
- `deadline`

## 6. Message types

ATP v0.1 defines:

- `request` — asks for information or work;
- `ack` — confirms receipt;
- `message` — ordinary conversational information;
- `data` — exchanges structured data or references;
- `question` — requests clarification;
- `progress` — reports partial progress;
- `rendezvous` — asks the other side to return later;
- `result` — provides a final or usable result;
- `error` — reports failure;
- `cancel` — cancels a request.

This vocabulary describes communication. It is not a remote command set.

## 7. Metadata record

A JSONL session MAY begin with a metadata record:

```json
{"protocol":"ATP","version":"0.1","type":"meta","session_id":"ses_01J...","created_at":"2026-10-20T18:30:00Z","participants":["ai","external"]}
```

Metadata records do not require `seq`.

## 8. Request example

```json
{
  "protocol": "ATP",
  "version": "0.1",
  "type": "request",
  "seq": 1,
  "message_id": "msg_01J...",
  "session_id": "ses_01J...",
  "request_id": "req_01J...",
  "trace_id": "tr_01J...",
  "from": "ai",
  "to": "external",
  "created_at": "2026-10-20T18:31:00Z",
  "reply_to_seq": null,
  "next_actor": "external",
  "status": "open",
  "body": {
    "text": "Check the requested information and report back."
  }
}
```

## 9. Handshake

The minimal handshake is:

```text
request -> ack
```

An `ack` confirms receipt only. It does not imply successful completion.

## 10. Rendezvous

When work cannot finish immediately, a participant may append a `rendezvous`:

```json
{
  "protocol": "ATP",
  "version": "0.1",
  "type": "rendezvous",
  "seq": 2,
  "message_id": "msg_02",
  "session_id": "ses_01",
  "request_id": "req_01",
  "from": "external",
  "to": "ai",
  "created_at": "2026-10-20T18:31:04Z",
  "reply_to_seq": 1,
  "next_actor": "ai",
  "status": "pending",
  "retry_after_s": 365,
  "next_check_at": "2026-10-20T18:37:09Z",
  "body": {
    "text": "Result not ready yet. Check again at the rendezvous time."
  }
}
```

`retry_after_s` and `next_check_at` are advisory scheduling metadata. The runtime or transport decides how and whether a participant is reactivated.

## 11. Cursor / incremental reads

A participant SHOULD retain the highest sequence it has processed:

```text
last_seen_seq = 14
```

On the next read it processes records where:

```text
seq > last_seen_seq
```

This cursor is local participant state and does not need to be written into every message.

## 12. Data exchange

Small structured values may be placed in `body.data`.

Larger or sensitive objects SHOULD be exchanged by reference:

```json
{
  "data_refs": [
    {
      "kind": "document",
      "ref": "transport-specific://object/123",
      "mime_type": "application/pdf"
    }
  ]
}
```

ATP core does not define how every `ref` is dereferenced. That belongs to a transport profile or extension.

## 13. Turn-taking

The Shared File MVP uses cooperative turn-taking.

- A writer SHOULD append only when `next_actor` names it, unless the preceding record allows `either`.
- Existing records MUST NOT be edited during normal exchange.
- A writer SHOULD read the current tail immediately before append.
- A transport SHOULD reject or surface stale writes instead of silently overwriting them.

More concurrent transports may define stronger semantics while preserving ordered message identity.

## 14. Capabilities and intents

ATP does not require a command library.

A message MAY carry an optional structured intent, but receiving an unknown intent MUST NOT become arbitrary execution.

Capability vocabularies are extensions layered above ATP.

## 15. Security boundary

ATP is a communication protocol, not a remote shell.

Message text MUST NOT be implicitly interpreted as authority to execute:

- shell or PowerShell commands;
- SQL;
- unrestricted filesystem operations;
- arbitrary code;
- privileged external actions;
- implicit authority escalation.

Execution, if supported by an implementation, requires a separately defined policy/capability layer.

## 16. Transport independence

Candidate transport profiles include:

- shared Git-backed file;
- local/shared filesystem;
- ClipBox;
- Gmail;
- Slack;
- dedicated mailbox services;
- future webhook or queue transports.

A transport profile specifies carriage, authentication, concurrency and object-reference behavior. It MUST NOT silently redefine ATP core message semantics.

## 17. Terminal outcomes

A logical request may terminate with:

- `result`
- `error`
- `cancel`
- expiry according to an explicit deadline or local policy

## 18. Versioning

ATP v0.1 is experimental.

Implementations MUST reject or explicitly negotiate protocol versions they do not understand. They MUST NOT silently reinterpret an unknown version as v0.1.
