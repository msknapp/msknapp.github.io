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

# Ports

- 0 reserved for root process
- 20 - FTP
- 22 - SSH
- 53 - DNS
- 80 - HTTP
- 88 - kerberos
- 143, 220 - IMAP
- 389 - LDAP
- 443 - HTTPS

## Port ranges that are blocked

- 0 to 1023: well known ports.  These can only be used by the root user, super user, or privileged users
- 1024 to 49151: registered ports.
- 49152 to 65535: private or dynamic ports.  Cannot be registered with IANA

# OS Signals

- INTERRUPT: SIGINT 2, sent by ctrl+c.  Processes should stop gracefully, but are not forced to, 
  they can just keep running in a different state.  This code implies it came from the user and not some other process, usually it means the application should stop.
- Send signals to processes using the “kill” command.
- STOP: 19 SIGSTOP, sent by ctrl+s and/or ctrl+z.  The process still exists but stops performing its function.
- CONTINUE: 18 SIGCONT, sent by ctrl+q.  After being stopped, this lets it resume performing its function.  This is also sent by the fg or bg commands.
- The trap command lets us control responses to signals.
- SIGTERM: 15, default sent by kill command.  Requests it to terminate.  Can run cleanup actions.  
  Usually this is sent by controlling processes, not users.  This is the most common signal used to start a graceful shutdown.
- SIGQUIT: 3 Like sigterm, but requests a core-dump too.  Use this when you also want some information 
  for debugging or troubleshooting later.  Can - run cleanup actions.
- SIGKILL: 9 forcefully terminates a program.  Processes cannot handle these or react to them.  
  It is impossible to run cleanup actions.  This is the - only way to stop a frozen or hung process.
- SIGHUP: indicates that the owning process has been stopped.

There are user defined signals, you might be able to use them to make processes behave a certain way or take some action.
Sometimes if you wrap your application in a bash script, signals may not be forwarded to your application, and it won’t be able to gracefully shut down.  You must write the script in a way that it traps SIGTERM and then forwards the signal to the process, e.g. a server.  Usually this requires running the server in the background, capturing its PID with “$!”, and then waiting for the server to finish.  A better option is to run the server directly from the container’s command, and not wrapping it from within a script.

# Exit codes

- Minimum of 0, max of 255.
- Returning a specific exit code can aid automation with deciding how to respond.  For instance, a controller may decide 
  if and when to retry a failure based on the exit code.
- View exit codes with “echo $?”
- 0: successful, truthy.  Anything non-zero is an indication of failure.
- 1: generic application failure, falsy
- Bash conventions:
  - 2: improper usage of the command, or file does not exist.
  - 126: cannot execute, due to permissions issue.  The file is not executable.
  - 127: file does not exist, or the command was not found on the PATH.
  - Responding to OS Signals, return 128 plus the signal number.
  - If interrupted with ctrl+c, the exit code should be 128+2 = 130.
  - Sigterm, 128+15 = 143
- It is recommended to not use exit codes above 125 except if they are from OS signals.  Don’t use exit code 2 in scripts or programs unless it was from parsing arguments.

# Viewing OS Info

- Number of cores: sysctl -n hw.ncpu
- Amount of RAM: sysctl hw.memsize
- Your architecture: uname -a
- Kernel Version: uname -a
- Number of processes: ps | wc
- Disk space: df, du
- Shell: echo $SHELL
- Operating System: uname

# Mac Shortcuts

Moving files in finder:
- Right click: use two fingers to press down on the scroll pad.
- Scroll on page: use two fingers to swipe on the scroll pad.
- Zoom: Use two fingers on the scroll pad, move your fingers apart or together.
- Select the files, choose to copy them with command+v, or right click and choose copy.
- Navigate to the folder where you want them placed.  Right click, if you hold down the option button, 
  the move command replaces the paste command, click move here.
- Swap workspaces: Place three fingers on the scroll pad and swipe left/right.
- Switch application windows: command+tab
- Scroll: use two fingers on the mousepad.
- Right click: press down with two fingers on the mouse pad.
- View hidden files from finder: shift+command+”.”

Removing a quarantine on a file: `xattr -r -d com.apple.quarantine <file>`

# Chrome Browser Shortcuts

Go to next or previous tab: command+option+left/right arrow.  Also: ctrl+tab to go right, add shift to go left.
Jump to tab: command+number.

# Common Environment Variables

- PATH: where commands are found, colon delimited.
- HOME: your home directory
- HTTP_PROXY, HTTPS_PROXY, similar: a URL that requests get proxied through.  curl and wget both use them.
- SHELL: absolute path to your shell, e.g. /bin/zsh
- USER: your user name
- PWD: the present working directory
- LANG: your language locale
- EDITOR: what to use for editing.  git uses it.
- HOSTNAME: DNS host name.
- TZ: time zone
- PS1: the prompt in your terminal, it's a template.  It supports various escape sequences:
  - \u=username, \h=short hostname, \H=full hostname, \w=absolute directory, \W=local directory
  - Use \$ for the end of the prompt, it's "$" for a normal user and "#" for root, so you should always use it.
  - default: `%n@%m %1~ %#`
  - the template can call for running commands too, but be sure to use quick running commands with brief output.
  - You can configure its color easily, which might be a good idea for different environments, like red for prod, green for non-prod, etc.
  - For local systems you probably don't need the user, host, time, etc. in the prompt.  For systems people ssh into, you probably should have that.
- DISPLAY: where to find your display.