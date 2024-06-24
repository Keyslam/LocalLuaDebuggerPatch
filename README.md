# Local Lua Debugger Patch
Patches LLD to not break inside a protected context

[Local Lua Debugger](https://github.com/tomblind/local-lua-debugger-vscode) is a (great) extension that adds debugging functionality to Lua; however, it comes with a simple flaw:
```lua
require("lldebugger").start()

local f = function()
    error() -- The edtior will break here
end

local succ, err = pcall(f)
```

In my opinion, if a `error` is caught by some other piece of code, there's no need for a debugger to break on it; it might even be counter productive if the error is used for control flow.

This library adds a quick monkeypatch to `pcall`, `xpcall`, `assert`, and `error` to prevent this behaviour.

### Usage
Simply add this library to your project, require it (instead of `lldebugger`) and call start on it:
```lua
local lldebuggerPatcher = require("path.to.this.library")
local lldebugger = lldebuggerPatcher.start()
```
It will in turn call `require("lldebugger").start()` and return it.

When using LÖVE, also call `lldebuggerPatcher.useLove()` at the end of your `main.lua`:
```lua
local lldebuggerPatcher = require("path.to.this.library")
local lldebugger = lldebuggerPatcher.start()

function love.errorhandler(msg)
    error(msg, 2)
end

function love.update()
    -- ...
end

lldebuggerPatcher.useLove()
```