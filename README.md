# tabpick

A tab picker for [herdr](https://herdr.dev). One key lists every tab across
every workspace, most recently used first, with a live preview of the pane you
would land in. Tabs are the only unit it deals in.

## Requirements

- herdr 0.7.4 or newer
- `fzf` and `jq` on `PATH`

## Install

```sh
herdr plugin install sushidesu/herdr-tabpick
```

Then bind a key in `~/.config/herdr/config.toml`:

```toml
[[keys.command]]
key = "prefix+t"  # any free key works
type = "shell"
command = "herdr plugin pane open --plugin sushidesu.tabpick --entrypoint open"
```

Reload with `herdr server reload-config`.

## Keys

| Key | Action |
| --- | --- |
| `j` / `k` | Move down / up |
| `l` / `enter` | Focus the tab, or create one in the selected directory |
| `h` / `q` / `esc` | Close the picker |
| `g` / `G` | First / last |
| `x` / `ctrl-x` | Close the selected tabs |
| `ctrl-n` | Make a new directory named by your query under the first configured root, and open a tab there — needs the `roots` file below |
| `tab` | Toggle multi-select |
| `/` | Search mode — every key types into the query; `esc` returns to nav mode and the query keeps filtering |

## Optional: tabs you have not opened yet

Point the picker at the directories your projects live in, and their
subdirectories join the list as tabs that do not exist yet — selecting one
creates the tab.

Name them in a `roots` file in the plugin's config directory, one per line:

`~/.config/herdr/plugins/config/sushidesu.tabpick/roots`

```
~/dev
~/work
```

`herdr plugin config-dir sushidesu.tabpick` prints that directory if yours
differs.

The immediate subdirectories of each root become rows. `ctrl-n` creates its
directory under the **first** root, so the order matters.

## How the order is tracked

herdr does not expose a last-focused timestamp, so the plugin records one: a
`tab.focused` hook touches one empty file per tab in the plugin's state
directory, and the picker reads them back in mtime order.

Right after installing, the order is cold — tabs you have yet to focus sort by
tab number.

## Troubleshooting

**`tabpick: fzf not found in PATH`** — herdr starts the plugin directly rather
than through a login shell, so the plugin sees the `PATH` the herdr server
itself was started with. Start herdr from a shell where `fzf` and `jq` already
resolve, or install them into a directory that this `PATH` already contains.

**The order never updates** — check that the hook is registered:

```sh
herdr plugin list --plugin sushidesu.tabpick
```

The plugin's line should read `enabled`:

```
- sushidesu.tabpick (tabpick) enabled [github:sushidesu/herdr-tabpick]
```

`No plugins installed.` means the install did not take; run the install command
again. `disabled` means it is registered but idle; switch it on with `herdr
plugin enable sushidesu.tabpick`.

## License

MIT
