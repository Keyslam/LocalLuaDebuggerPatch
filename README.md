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
Simply add this library to your project and require it instead of `lldebugger`:
```lua
local lldebugger = require("path.to.this.library")()
-- Or when using LÖVE:
local lldebuger == require("path.to.this.library")(true)
```
It will in turn call `require("lldebugger").start()` and return it.