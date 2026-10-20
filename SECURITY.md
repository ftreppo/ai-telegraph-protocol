# Security Policy

ATP is a communication protocol, not an execution authority.

## Core security rule

Receiving an ATP message MUST NOT, by itself, grant permission to execute arbitrary code, shell commands, SQL, filesystem operations, privileged actions, or external side effects.

Implementations should fail closed when they encounter:

- unknown protocol versions;
- malformed messages;
- unknown privileged extensions;
- stale or conflicting writes;
- unsupported data-reference schemes;
- requests that exceed the receiver's local policy.

## Sensitive data

Avoid putting secrets directly into public examples, logs, issues or test vectors.

Transport profiles should document:

- who can read and write;
- confidentiality guarantees;
- integrity guarantees;
- retention behavior;
- replay risks;
- how identities are authenticated.

## Reporting vulnerabilities

Please avoid publishing exploit details in a public issue before a mitigation is available. Use GitHub's private vulnerability reporting feature for this repository when available, or contact the repository owner privately through GitHub.
