---
title: 
weight: 10
description: 
summary: 
lastmod: 2025-08-13
date: 2025-08-13
tags: []
categories: []
series: []
keywords: []
---

## VSCode
- Open source, from Microsoft, only free IDE for Golang.
- Stop the popups: In settings, search for editor.parameterHints.enabled, set it to false.
- Shortcuts (Mac):
  - View Shortcuts: command+k, then command+s
  - View Reference Shortcuts: command+k, then command+r
  - View Command Palette: command+shift+p
  - AutoComplete: Ctrl+space
  - View Docs: command+k, then command+i
  - Go to source: F12
  - Peek at Definition: option+F12
  - Replace in files: Cmd+option+h
  - replace in current file: Cmd+option+f
  - Switch to left tab: command+shift+[
  - Switch to previous tab: command+shift+]
  - Swap line up/down: option + arrow up/down
  - Delete Current Line: shift+command+k
  - Collapse Current block: option+command+[ (it’s called folding)
  - Expand Current block: option+command+]
  - Unfold all: command k, then command j
  - Back: control+”-”
  - Forward: control+shift+”-”
  - Insert Line below: command+enter
  - Insert Line above: command+shift+enter
- Configuring Tests: write a launch.json file.  In the “configurations” section:
- Each entry can have:
  - Name
  - Type: relates to the language or debugger, “go” or “debugpy”
  - Request: “launch” the process, or “attach” to an existing process.  
    You probably should use “launch” unless developing a webpage or - testing a running server.
- Most Can have:
  - Cwd: current working directory
  - Args: arguments to send to the debugger
  - Program: what executable to run.  More common for interpreted languages, it’s the program or script that the debugger runs.
- Golang: also has “mode” and “program”.  It’s unclear what mode does, stick with “auto”
- Python
- Type: debugpy
- Program: what script to run.
- Variables:
  - file: absolute path to the active file.
  - fileBasename: the base name of the current file, so just the name.
  - fileDirname: the absolute path to the directory holding the current file.
  - workspaceFolder: absolute path to the root folder opened in vscode.
  - relativeFile: path to current file relative to the workspace folder.
