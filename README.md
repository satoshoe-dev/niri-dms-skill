# niri-dms skill

An Agent Skill for customizing a desktop that runs the
[niri](https://github.com/niri-wm/niri) compositor with
[DankMaterialShell](https://github.com/AvengeMedia/DankMaterialShell) (DMS).
It tells a coding agent (Claude Code, Codex, OpenCode and others that read
`SKILL.md` files) where things live, which commands exist, which files it must
not touch, and how to check its work.

It is meant for people who ask their agent things like "make the gaps
smaller", "bind Super+E to my file manager", "why are my GTK apps not using
the wallpaper colors" or "the bar is gone".

## What it covers

```
skills/niri-dms/
├── SKILL.md            # entry point: when to use, safety rules, decision framework, examples
├── niri.md             # config layout, includes, binds, window/layer rules, blur, animations, outputs
├── dms.md              # dms CLI, IPC discovery, settings files, bar, control center, desktop widgets
├── theming.md          # themes, wallpaper, light/dark, matugen, GTK/Qt/terminal colors
├── plugins.md          # installing, enabling and configuring DMS plugins
├── pitfalls.md         # known traps with symptoms and fixes
└── troubleshooting.md  # dms doctor, logs, bar gone, niri config errors, after updates
```

Out of scope: writing DMS plugins (use the official
[dms-plugin-dev](https://github.com/AvengeMedia/DankMaterialShell/tree/master/.agents/skills/dms-plugin-dev)
skill), developing niri or DMS, and installing either from scratch.

## Install

With the [skills CLI](https://github.com/vercel-labs/skills):

```bash
npx skills add ./niri-dms-skill -g            # local clone, user-wide
npx skills add satoshoe-dev/niri-dms-skill -g  # from GitHub
```

By hand, copy or symlink the skill folder into your agent's skills directory:

```bash
# Claude Code, for all projects
mkdir -p ~/.claude/skills
ln -s "$PWD/skills/niri-dms" ~/.claude/skills/niri-dms

# agents that read .agents/skills (Codex, OpenCode, and others), per project
mkdir -p .agents/skills
cp -r skills/niri-dms .agents/skills/
```

The plugin development skill from DMS installs the same way:

```bash
npx skills add AvengeMedia/DankMaterialShell --skill dms-plugin-dev -g
```

## Safety model

The skill asks the agent to:

- never edit files owned by packages (the `dms` and `niri` binaries, the DMS UI
  embedded in `dms`, system plugins, `/etc/niri/config.kdl`)
- leave the files DMS generates in `~/.config/niri/dms/` alone and override
  them in the user's own config instead
- copy a file with a timestamp before editing it, or use `dms backup create`
- run `niri validate` and `jq empty` after edits and report the result
- ask before `dms restart`, stopping the service, resets, restores, lock or
  power actions
- prefer read-only commands (`dms ipc list`, `settings get`,
  `niri msg --json ...`) for finding things out
- treat plugins and extra registries as code that runs with full user
  permissions

## Versions

Written against niri 26.04 and DMS 1.6.3, and checked against the upstream
sources and docs of both. Features newer than a given release carry the
version that introduced them, for example `include` (niri 25.11),
`place-within-backdrop` (niri 25.05), background blur (niri 26.04), and
`dms backup` and plugin registries (DMS 1.6.0). On other versions the skill
tells the agent to check `--help` and `dms ipc list` before relying on a
command.

Corrections and additions are welcome, especially for behavior that changed
in a newer release.

## Credits

- The structure follows the agent skill that ships with
  [Omarchy](https://github.com/basecamp/omarchy): a broad trigger
  description, topic guides next to `SKILL.md`, safety rules up front, a
  decision framework and example requests mapped to commands.
- Facts come from the [niri wiki](https://github.com/niri-wm/niri/wiki), the
  [DMS documentation](https://danklinux.com/docs/dankmaterialshell/) and the
  DMS source.
- Written with help from Claude (Anthropic).

## License

MIT, see [LICENSE](LICENSE).
