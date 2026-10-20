# Non-goals

ATP v0.1 is intentionally small.

It does **not** attempt to define:

- a universal remote procedure call system;
- a remote shell;
- arbitrary command execution;
- an authorization framework for privileged actions;
- a general job scheduler;
- a workflow engine;
- a distributed database;
- a replacement for HTTP, email, Slack, queues or other transports;
- model prompting conventions;
- a universal capability ontology;
- a mechanism that guarantees an AI runtime can wake itself at a future time.

Those concerns may be implemented above or below ATP.

ATP core focuses on message identity, ordering, acknowledgement, rendezvous, correlation and data references.
