# telescope-smart-history.nvim

A history implementation that memorizes prompt input for a specific context.
This means that each prompt input is associated with a calling picker and cwd.

So if you work on multiple projects and cycle back it will only show input that
was used in this Project.

## Here's how it works:

1. Whenever you type something into a Telescope prompt (like searching for "config" in `find_files`), that search term is saved

2. The default Telescope history just saves all search terms globally. The smart-history extension instead saves:
   - What you typed
   - Which picker you were using (`find_files`, `live_grep`, etc.)
   - Which directory you were in

   See `:help telescope.defaults.history` for more info.

3. You can cycle through this history using:
   ```lua
   ["<C-Down>"] = require('telescope.actions').cycle_history_next,
   ["<C-Up>"] = require('telescope.actions').cycle_history_prev,
   ```

For example:
- If you're in **project A** and search for "router" in `live_grep`
- Then switch to **project B** and search for "database"
- When you come back to **project A** and use `live_grep` again, pressing Ctrl-Up will show "router" not "database"

This is different from:
* `telescope.builtin.oldfiles()` which shows your **previously opened files**. For example, if you opened `init.lua` and `plugins.lua`, those files will show up in oldfiles. The `only_cwd` option lets you limit this to files from your current working directory.
* [telescope-frecency.nvim](https://github.com/nvim-telescope/telescope-frecency.nvim) which ranks files based on both **frequency** and **recency** of access. It combines how often you access a file with how recently you accessed it. For example, a file you edit multiple times a day will rank higher than one you opened once last week. This is similar to how modern browsers rank URLs in their address bar suggestions.

## Setup

It requires [sqlite.lua](https://github.com/tami5/sqlite.lua), because its easier
and faster than having a flat text file that keeps track of input + picker + cwd.

It will be configured with the same keys as the normal history configuration.

```lua
local actions = require("telescope.actions")

telescope.setup {
  defaults = {
    history = {
      path = '~/.local/share/nvim/databases/telescope_history.sqlite3',
      limit = 100,
    },
    mappings = {
      i = {
        -- Another example using Ctrl-j and Ctrl-k
        ["<C-j>"] = actions.cycle_history_prev,
        ["<C-k>"] = actions.cycle_history_next,
      },
    },
  }
}


require('telescope').load_extension('smart_history')
```
