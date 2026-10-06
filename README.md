# nvim

My Neovim configuration. C++ first, with LaTeX, Markdown, and Lua along for the
ride. Managed with [lazy.nvim](https://lazy.folke.io).

## Requirements

Neovim 0.11 or newer, since per-server LSP setup goes through `vim.lsp.config`.
Developed on 0.12.

Language servers install themselves. Mason is pinned to `clangd`,
`rust_analyzer`, `bashls`, `lua_ls`, `marksman`, `pylsp`, `jsonls`, and
`texlab`, and enables them automatically on first launch.

Formatters, linters, and debug adapters that Mason can provide (stylua,
taplo, goimports, hadolint, gitlint, debugpy, cpptools) install themselves in
the background on startup too; the list is in `lua/config/mason_tools.lua`.

One exception: `cmake-language-server` comes from pipx and is expected on
`PATH`. Its virtualenv needs `pygls>=1.1.1,<2.0`, because pygls 2.x dropped the
import that server relies on.

## Layout

```
init.lua              entry point; ordered requires, nothing else
lua/config/lazy.lua   lazy.nvim bootstrap and global plugin options
lua/config/keymaps.lua
lua/config/options.lua
lua/config/autocmds.lua
lua/plugins/          one file per plugin spec
after/ftplugin/       per-filetype settings
after/indent/         per-filetype indent overrides
syntax/               syntax for filetypes Neovim doesn't ship
queries/              treesitter query overrides
tests/                unit + smoke specs (see Testing)
```

Load order in `init.lua` is deliberate. Keymaps run first because they set the
leader key, which lazy.nvim needs before it registers any plugin `keys` spec.
Options run after plugins so they override plugin defaults. Autocommands and
diagnostics run last.

## Install

```bash
git clone https://github.com/Derrekito/nvim ~/.config/nvim
nvim
```

Plugins install on first launch.

Two of the plugins are mine, [devdocs.nvim](https://github.com/Derrekito/devdocs.nvim)
and [diagnostic-picker.nvim](https://github.com/Derrekito/diagnostic-picker.nvim).
They install from GitHub like anything else. On a machine where a local checkout
exists under `~/devel` or `~/Projects`, lazy.nvim uses that instead, so edits are
live. See the `dev` block in `lua/config/lazy.lua`.

## Testing

```bash
tests/run.sh          # everything
tests/run.sh unit     # modules in isolation, no plugins (fast)
tests/run.sh smoke    # the real config with every plugin loaded
tests/run.sh smoke tests/smoke/lsp_spec.lua   # one file
```

Specs use [plenary.nvim](https://github.com/nvim-lua/plenary.nvim)'s busted
flavor. `tests/unit/` runs each spec under `tests/minimal_init.lua` (this
config's `lua/` plus plenary, nothing else). `tests/smoke/` starts a fresh
`nvim` per file with the real `init.lua`: startup is clean, every plugin
loads, keymaps exist without prefix clashes, each filetype opens without
errors, UIs open and close, the config-reload-on-save doesn't stack
autocmds, and clangd attaches and restarts end to end. Editor state
(shada, logs) goes to a throwaway `XDG_STATE_HOME`.

Missing external tools (formatters, linters, debug adapters) show up as
*pending*, not failures: they're per-machine installs.

## Colors

[Rosé Pine Moon](https://rosepinetheme.com), with a local palette override in
`lua/rose-pine-moon.lua`.
