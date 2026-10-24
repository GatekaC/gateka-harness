# gateka/ — what Gateka adds to the harness

This checkout is `GatekaC/gateka-harness`, a fork of
[deepseek-ai/deepseek-harness](https://github.com/deepseek-ai/deepseek-harness)
(DeepSeek Harness, `dsh`, MIT). It is the **`gateka` work engine** of the
Gateka platform: the chat corridor hands it a brief and the product's
repositories (`gateka-core/gateka/bricks/agents/capability/llm.py`,
`engine_command` / `harness_result`), it answers with the `GATEKA_RESULT`
line last.

## The one rule

**Upstream files are never edited.** Everything Gateka adds lives in this
directory — profiles, plugins, this README — so that

```sh
git fetch upstream && git rebase upstream/master
```

stays a no-conflict operation against a developer preview that warns it
will break compatibility. A change that needs an upstream file is a
plugin or a profile patch here, or a pull request upstream.

## Layout

- `profiles/nebius.patch.yml` — the Nebius AI Studio route (OpenAI-compatible,
  `NEBIUS_API_KEY`) and the default model, by task route
  (`gateka-data/architecture/decisions/0210-*.md`). The run passes it with
  `--patch`; the default model is what `harness_model()` meters.
- `plugins/` — Gateka's own plugins, when needed (the result contract, the
  brick-context loader, `bin/publish-paths` as the only commit door).
- `bricks/<brick>.yml` — one profile per brick (what to read first, the
  latency budget, route overrides). One harness, one version; the
  variation per brick is a file here, never a fork.

## Build on the beta host

```sh
corepack pnpm install --frozen-lockfile
corepack pnpm run build:native-system   # the Landlock/flock addon, cc
corepack pnpm run build:lib:host        # the host libraries (tsc)
```

Node ≥ 24.9, pnpm through corepack (never a global install). The engine
is available to the corridor when `apps/cli/src/bin.ts`, the tsx loader,
the built native addon and `gateka/profiles/nebius.patch.yml` exist
(`llm.available_engines`).
