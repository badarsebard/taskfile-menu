# Taskfile Menu

A drop-in starter pair for new projects using [Task](https://taskfile.dev):

- **`Taskfile.yaml`** — the entrypoint where you define your project's tasks.
- **`Taskmenu.yaml`** — flattened include that adds an interactive `fzf` task picker as the default task.

Running `task` with no arguments launches a hierarchical picker over every task defined in `Taskfile.yaml`, with tasks grouped into submenus based on `:`-separated prefixes.

## Quick start

1. Copy `Taskfile.yaml` and `Taskmenu.yaml` into a new project.
2. Install the prerequisites (see below).
3. Add your own tasks under the `tasks:` key in `Taskfile.yaml`.
4. Run `task` to launch the menu.

### Experimental
Task has an experimental feature that lets you use remote Taskfile.yaml entrypoints. The `Taskmenu-install.yaml` file in this repo contains a task to simplify initializing the `Taskfile.yaml` and `Taskmenu.yaml` files in a new repo. You can execute it with the following command:
```bash
TASK_X_REMOTE_TASKFILES=1 task --yes --taskfile https://raw.githubusercontent.com/badarsebard/taskfile-menu/refs/heads/main/Taskmenu-install.yaml
```

**Warning:** this will overwrite any existing `Taskfile.yaml` in the directory.

If you're adding the menu to a project that already has a `Taskfile.yaml`, invoke the `menu-only` task instead — it fetches only `Taskmenu.yaml`. Then add `Taskmenu.yaml` to the `includes` of your existing `Taskfile.yaml`, and if you plan to flatten it, check for an existing `default` task first.

For security, we **strongly** recommend forking this repository and running the command against your fork — replace `badarsebard/taskfile-menu` in the URL with your own slug. That way you only run code you control.

## Prerequisites

- [Task](https://taskfile.dev/installation/)
- `fzf`, `jq`, `awk`

macOS:

```sh
brew install go-task fzf
```

Debian/Ubuntu:

```sh
sudo apt-get install fzf jq
# install task: https://taskfile.dev/installation/
```

If any of `fzf`, `jq`, or `awk` are missing, the default task prints a notice naming the missing tool(s) with OS-specific install hints and falls back to `task --list`.

## How the menu works

- Tasks are discovered from `task --list-all --json`.
- Names are split on `:` to build a tree — e.g. `deploy:dev` and `deploy:prod` appear under a `deploy/` submenu.
- A task that is *both* runnable itself and a menu prefix for other tasks shows up as two entries side by side: the task itself, and a `name/` submenu containing its children.
- `..` at the top of any submenu goes back one level.
- Type to fuzzy-filter entries in the current menu by name; matches update as you type.
- Enter runs the selected task; Esc exits the picker.

## Adding tasks

Edit `Taskfile.yaml` and drop tasks under `tasks:`. They'll appear in the menu automatically. The `flatten: true` on the include means `Taskmenu.yaml`'s `default` task becomes the parent's default, so bare `task` always opens the picker.

## Files

- `Taskfile.yaml` — project entrypoint. Includes `Taskmenu.yaml`. Home for your tasks.
- `Taskmenu.yaml` — `default` task (fzf picker with a soft fallback to `task --list`). Leave as-is unless you're customizing the picker.
