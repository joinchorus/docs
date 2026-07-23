# RFC-001: Native Git Storage Engine & Audit Log

- **Status:** Approved / Implemented (`v0.1.0-alpha`)
- **Author:** Senior Software Architect
- **Target Repository:** [chorus-project/chorus](https://github.com/chorus-project/chorus)
- **Domain:** `chat.joinchorus.app`

---

## 1. Executive Summary

Chorus uses a native Git repository as its immutable storage engine and audit log. Every thread creation and message append is represented as a cryptographic Git commit containing structured JSON documents.

---

## 2. Repository Layout

```
./data/repository/
├── .git/
├── identities/
│   └── <identity_id>.json
├── threads/
│   └── <thread_id>/
│       ├── index.json
│       └── messages/
│           └── <message_id>.json
```

---

## 3. Commit Convention

Commit messages follow a strict deterministic format:

```
thread: create <thread_id>
message: append <message_id>
identity: create <identity_id>
index: update <topic_id>
```

---

## 4. Security & Immutability

Because Git commits form a Merkle DAG linked by cryptographic SHA hashes, any attempt to tamper with past thread history invalidates downstream hashes, guaranteeing tamper-proof audit trails.
