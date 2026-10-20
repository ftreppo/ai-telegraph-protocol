# Shared File Transport Profile

**Profile status:** experimental draft for ATP v0.1

This profile carries one ATP session in one UTF-8 JSON Lines (JSONL / NDJSON) file.

## Representation

- One JSON object per line.
- The first line MAY be a `meta` record.
- Subsequent lines are ordered ATP messages.
- Normal operation is append-only.

## Reader behavior

A reader:

1. opens the journal;
2. parses complete lines;
3. verifies protocol/version and required fields;
4. ignores already processed messages using its local `last_seen_seq`;
5. processes only messages addressed to it or explicitly broadcast by a future extension.

A partially written final line MUST NOT be treated as a valid complete message.

## Writer behavior

Before appending, a writer SHOULD:

1. read the current complete tail;
2. verify that it is permitted to write according to `next_actor`;
3. compute `seq = previous_seq + 1`;
4. append exactly one complete JSON object followed by a newline;
5. ensure the append did not overwrite or discard a concurrent writer.

## Concurrency

The profile requires stale-write detection.

For Git-backed storage, the writer can use the current blob/commit identity as optimistic concurrency control.

For a local/shared filesystem, an implementation should use an atomic append or a lock appropriate to that filesystem.

If the writer discovers that another complete message was appended after its last read, it MUST re-read the tail and recompute its append. It MUST NOT silently overwrite the newer message.

Detailed concurrent-append semantics are still under discussion.

## Cursor

`last_seen_seq` is participant-local state. It is not part of the shared journal unless an implementation explicitly chooses to persist it elsewhere.

## Security

Filesystem or repository permissions define who can read and append. This profile does not add encryption by itself.

Do not place secrets in a shared journal unless the storage and access policy are appropriate for those secrets.
