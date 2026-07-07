<p align="center"><img src="https://raw.githubusercontent.com/go-proc/brand/main/social/go-proc.png" alt="go-proc" width="640"></p>

<h1 align="center">go-proc</h1>
<p align="center"><strong>Pure-Go process-lifecycle primitives — reap, supervise, respawn. No cgo.</strong></p>

<p align="center">
  🌐 <a href="https://go-proc.github.io">Website</a> ·
  📚 <a href="https://go-proc.github.io/docs/">Documentation</a>
</p>

<p align="center">
  <a href="https://go-proc.github.io/docs/"><img alt="Docs" src="https://img.shields.io/badge/docs-mkdocs--material-B45309?style=flat-square"></a>
  <a href="https://github.com/go-proc/supervisor/blob/main/LICENSE"><img alt="License: BSD-3-Clause" src="https://img.shields.io/badge/license-BSD--3--Clause-blue?style=flat-square"></a>
  <img alt="Go 1.26.4+" src="https://img.shields.io/badge/go-1.26.4%2B-00ADD8?style=flat-square&logo=go&logoColor=white">
  <img alt="Coverage 100%" src="https://img.shields.io/badge/coverage-100%25-1a7f37?style=flat-square">
</p>

---

go-proc is a set of **pure-Go (no cgo) primitives for supervising the lifecycle
of processes** — the small, reusable pieces an init or a workload manager needs:
a PID-1 subreaper, a process supervisor, and a restart/reconcile state machine.
They were extracted from a microVM init and **generalized** so they depend on no
particular workload model, transport, or scheduler.

> **Reap orphans. Supervise children. Respawn on your terms.**

Each library is CGO-free, dependency-free, **100% test-covered** (including error
branches), race-tested, and green across the six 64-bit Go targets
(amd64, arm64, riscv64, loong64, ppc64le, s390x).

## Repositories

| Repo | What it is |
|------|------------|
| [**supervisor**](https://github.com/go-proc/supervisor) | a **PID-1 subreaper** (`SIGCHLD` + `Wait4`, Linux, with a portable no-op stub elsewhere) **and** a process supervisor that drives a pluggable `Runtime` over a `Spec` of `Proc`s, correlates exits back to the owning process, and restarts per `RestartPolicy` |
| [**respawn**](https://github.com/go-proc/respawn) | a **restart / reconcile state machine** — grace-period debounce (anti-flap), `max_restarts` sliding-window anti-thrash, and constant/exponential backoff — a pure `Decide` core wrapped in a concurrent per-unit `Reconciler` |
| [**docs**](https://github.com/go-proc/docs) | MkDocs Material documentation, versioned with [mike], served at [/docs/](https://go-proc.github.io/docs/) |
| [**go-proc.github.io**](https://github.com/go-proc/go-proc.github.io) | the Hugo landing page |
| [**brand**](https://github.com/go-proc/brand) | logos and brand assets |

## Principles

- **Pure Go, zero cgo.** Cross-compiles and embeds anywhere; a static binary by
  default. Linux-specific syscalls (the subreaper) sit behind build tags with a
  portable stub, so `go build` / `go vet` stay green on every OS.
- **Generic, not weft-shaped.** No dependency on any workload schema, gRPC, or
  health checker — a `Runtime` interface, an `Actions` interface, and local
  value types are the only seams.
- **A pure decision core.** Scheduling is a pure function of
  `(policy, history, signal, now)`; the concurrency lives in a thin shell around
  it, so every decision is unit-testable against a fixed clock.
- **100% test coverage**, including every error branch, is the target — enforced
  as a CI gate on each library.

## Status

**Both primitives shipped.** `supervisor` (subreaper + supervisor) and `respawn`
(restart state machine) are released at 100% coverage, race-tested, `gofmt` +
`go vet` clean, CI green across all six 64-bit Go arches.

BSD-3-Clause.

[mike]: https://github.com/jimporter/mike
