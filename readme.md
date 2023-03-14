# taskwarrior.nvim

`taskwarrior` is probably the best tool for managing tasks and tracking work
time. This is an attempt to make it even better and more convenient for
`neovim` users. (although `orgmode` users may disagree)".

## Features

- [Taskwarrior Command](#taskwarrior-command)
- [Telescope](#telescope)
- [Task monitoring](#task-monitoring)

## Usage

### Installation

Install the plugin with your preferred package manager:

### [packer](https://github.com/wbthomason/packer.nvim)

```lua
use("ribelo/taskwarrior.nvim")
require("taskwarrior.nvim").setup({
    -- your configuration comes here
    -- or leave it empty to use the default settings
    -- refer to the configuration section below
})
```

#### lazy.nvim

```lua
{
  "ribelo/taskwarrior.nvim",
    opts = {
      -- your configuration comes here
      -- or leave it empty to use the default settings
      -- refer to the configuration section below
    }
}
```

### ⚙️ Configuration

`taskwarrior.nvim` comes with the following defaults:

```lua
{
  filter = { "noice", "nofile" }, -- Filtered buffer_name and buffer_type.
  task_file_name = ".taskwarrior.json", 
  -- After what period of time should a task be halted due to inactivity?
  granulation = 60 * 1000 * 10,
  notify_start = true, -- Should a notification pop up after starting the task?
  notify_stop = true,
  notify_error = true,
}
```

### Taskwarrior Command 

Arguments are directly passed to the `task`, and `stdout` is passed to the notification.

```lua
:Task add Find the adjustable wrench project:Home priority:H
```
### Telescope

The function `browser` is exported, which takes an array of arguments that is
passed to the `task export` command, which is usually `ready` report. 

```lua
require("taskwarrior_nvim").browser({"ready"})
```

```lua
mappings = {
    -- add task
    ["<M-a>"] = taskwarrior.cmd({"add", unpack(vim.split(vim.fn.input("Custom command: "))))
    ["<M-S-d>"] = task:delete 
    ["<M-d>"] = task:done
    ["<M-s>"] = task:toggle or task:stop -- toggle
    ["<M-y>"] = vim.fn.setreg("+", task.uuid) -- yank uid to default register
    -- run custom command on task
    ["<M-c>"] = task:cmd(vim.split(vim.fn.input("Custom command: "))) 
  }
```

### Task Monitoring

`taskwarrior_nvim` allows for automatic time tracking based on the
`config.task_file_name` file - `.taskwarrior.json` is the default value. The
configuration file is searched for in `cwd`, and tasks are automatically
switched based on the `BufEnter` event. `cwd` is cached, so changing tasks
after opening or changing a buffer within a visited project in one session is
lightning-fast and does not consume resources.

#### .taskwarrior.json

```javascript
{
  // uuid: if the task is clearly defined, UUID can be used 
  "uuid": "7a8a5711-48c6-4667-ab27-2fec7d6b6051",
  // description: is ALWAYS a collection of maps."
  "description": [
    {
      // Command: is a command that is called from the command line as is and its
      // output is taken from stdout. It is always a list of arguments passed to the
      // CLI."
      "command": [
        "git",
        "remote",
        "get-url",
        "origin"
      ],
      // regex: allows matching a part of stdout. As it's Vim.regex, it's magical!
      "regex": "https://github.com/([^/]+/[^/]+)%.git"
    },
    {
    // text: allows adding static texts.
      "text": ":"
    },
    {
      "command": [
        "git",
        "rev-parse",
        "--abbrev-ref",
        "HEAD"
      ]
    }
  ],
  // project: are always combined into one since Taskwarrior only permits
  // assigning a single project to one task.
  "project": [
    {
      "text": "ribelo"
    }
  ],
  // tags: are always connected in a string, separated by commas because
  // Taskwarrior allows assigning multiple tags to one task."
  "tags": [
    {
      "text": "nvim_plugins"
    }
  ]
}
````
