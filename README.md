# AuraBaba WorkBuddy Connector

This repository contains the WorkBuddy connector package for AuraBaba project
management.

It uses WorkBuddy's **CLI + Skill** connector format. AuraBaba already ships a
cross-platform `aura` CLI, so the connector delegates installation,
authentication, status checks, and logout to that supported CLI. The Skill
then explains the safe command sequences for issues, projects, agents, squads,
and autopilots.

The package intentionally uses one WorkBuddy connection method. It does not
claim an AuraBaba remote MCP endpoint that has not been published or verified.
When an official MCP endpoint is available, it should be submitted as a
separate connector source, as required by WorkBuddy's connector rules.

## Connector files

```text
.
├── connector-meta.json
├── cli.json
├── icon.svg
└── skills/
    └── aurababa-platform/
        └── SKILL.md
```

The directory can be submitted to WorkBuddy using the official
[connector guide](https://open.workbuddy.cn/docs/connector).

## User setup

After WorkBuddy installs the connector, its connection flow runs the official
AuraBaba CLI. Users sign in through the browser, choose a workspace, and then
the Skill can operate that workspace. API tokens and CLI state remain under
the user's local AuraBaba configuration; no credentials belong in this repo.

AuraBaba: <https://aurababa.com>

AuraBaba CLI source: <https://github.com/lingling1989r/AuraManager>

## Local validation

Run the following checks before submitting a new version:

```bash
python3 -m json.tool connector-meta.json >/dev/null
python3 -m json.tool cli.json >/dev/null
test -s icon.svg
test -s skills/aurababa-platform/SKILL.md
```

Then verify the CLI lifecycle on macOS, Linux, and Windows: install, browser
authentication, `aura auth status`, and `aura auth logout`.

## License

MIT-0. See [LICENSE](./LICENSE).
WorkBuddy connector for AuraBaba project management
