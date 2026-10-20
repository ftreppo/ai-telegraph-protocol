# Contributing to ATP

Thanks for helping test and improve the AI Telegraph Protocol.

ATP is still an experimental draft. Early contributions are especially useful when they expose ambiguous semantics, interoperability problems, race conditions, security assumptions, or transport-specific leakage into the core protocol.

## Before opening a pull request

For changes to the wire format or protocol semantics, please open an issue first.

A useful proposal should explain:

1. the problem;
2. a concrete example;
3. whether the change belongs in the core protocol or in a transport profile;
4. backward-compatibility impact;
5. security implications;
6. test vectors that should pass or fail.

## Pull requests

Changes to the protocol or schema should normally include:

- an updated specification section;
- JSON Schema changes when applicable;
- at least one valid example;
- at least one invalid or edge-case example where useful.

Keep reference implementations deliberately small. ATP should be implementable without adopting a large framework.

## Scope

Good contribution areas include:

- protocol semantics;
- mailbox/journal behavior;
- handshake and acknowledgement rules;
- rendezvous and retry behavior;
- correlation and idempotency;
- concurrency and stale writes;
- data references;
- transport profiles;
- security analysis;
- interoperability test vectors;
- minimal reference implementations.

Capability vocabularies and executable actions are extensions, not ATP core.

## Conduct

Be precise, constructive and willing to separate protocol requirements from implementation preferences.
