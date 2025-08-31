# Command Line

## JQ Expressions

Naturally it validates json and pretty prints it.
Command line arguments:

- Use “-c” to print compact results, not pretty printed.
- Use “-r” for raw output, not formatted as json.  Strings won’t be quoted.
- Pass variables with –arg name value
- Expressions:
    - “.” is the identity operator.  It copies the input to the output. means self, text selects a key, delimit keys with “.” to select sub-objects.
      You can also select values of keys using quotes or square brackets.
    - .key returns the value of the key.  You can quote the key, and wrap it in square brackets too. 
      Append a “?” to make jq not error if the key is missing.
    - Use “|” to feed results to another filter.  If the left side is a generator, then the right side is run once for each generated input.
    - Select array items with “.[n]” where n is the index.  Slice an array with “.[start:end]”, end exclusive.  It is a generator.
    - Select all items in an array with “.[]”.  It will iterate over them.  Works on objects too, but only iterates over the values, not keys.  
      It is a generator.
    - Commas: input is fed to each filter delimited by commas, and then their outputs are concatenated.  It is a generator.
    - Put “?” after keys or selections so jq will not error if they are missing.
    - Use “..” to recursively descend into an object.
    - Arbitrary values can be used as filters too, they ignore the input and yield their own value as output.
    - Output json in its usual format “{foo: .foo, bar: [ .bar, .baz]}”, etc.
- Useful functions:
    - Has, in, contains, inside
    - Split(delimiter), join(delimiter)
    - All(condition), any(condition), endswith(str), startswith(str)
    - Unique
    - length
    - select(expression) - removes records that don’t match
    - map(filter)
    - To_entries: converts to an array of key/value pairs like {“key”: key, “value”: value}
- You can use normal comparisons like “==”, “<”, “>”, “<=”, “>=”, etc.
- Cannot use “||” or “&&”, use “and”, “or”, and/or “not”.
- There is a “//” operator which acts somewhat like ternary, or a defaulting operation. 
  If every value on the left of the operator is false or null, then it produces all values on the right of the operator.
  It naturally filters out false or null values on the left.

## Terminal Multiplexer

The `tmux` command.

- A tmux session holds one or more windows.  Sessions have names, not indexes.  Windows can be named, but using their index is common too.  
  Only one window is shown in a terminal at a time, but you can switch between them.  Windows are segmented into sub-regions called “panes”.  
  Only one pane is active at any time, the active pane receives keyboard input.  
  It is possible to have a single window be a member of multiple tmux sessions, but this is uncommon.  
  Sessions can be attached to by multiple client terminals, even from different users.  Clients attach or detach to sessions.
- Create a session: tmux new [-s name]
- From within a session, send commands to tmux: control+b, then a command.
- From within a session, issue tmux sub-commands: control+b, then “:”, then you enter named tmux sub-commands.
- Help on session commands: control+b, then “?”
- Detach from session: control+b, then d
- Re-attach: tmux attach [-t sessionname]
- Making Your Layout:
    - New window: ctrl+b, “c”
    - Delete current window: ctrl+b, “&”
    - Split Pane vertically (horizontal line splits top/bottom): ctrl+b, then the double quote character.  Or ctrl+b, “:”, “split-window -v”
    - Split pane horizontally (vertical line splits right/left): ctrl+b, “%”  Or ctrl+b, “:”, “split-window -h”
    - Remove current pane: ctrl+b, “x”
    - Swap panes: ctrl+b, then “{“ or “}”
    - Resize panes in small steps: ctrl+b, then ctrl+arrow key.
    - Resize panes in large steps: ctrl+b, then option+arrow key.
- Navigating:
    - Switch Window: ctrl+b, then the number for the window.
    - Next/previous window: ctrl+b, “n” or “p”
    - Switch pane: ctrl+b, then use arrow keys.
- Copy and paste: ctrl+b, then “[“ to copy, or “]” to paste.  Does not go to system clipboard.
    - From copy mode (ctrl+b, “[“) use:
    - Ctrl+space to start selection.
    - Arrow keys to move around.
    - Ctrl+w to accept the selection.
    - “q” to abort copying.
    - Getting to system clipboard:
        - Easiest method is “pbcopy <<< “ then ctrl+b, “]”.  Or use echo and pipe it into pbcopy.  Pasted text might need to be quoted.
- Usefulness: Tmux is most useful when some panes are tailing logs, monitoring resource usage, or monitoring changes in configuration, 
  while another pane is used to command things.  Put panes on the same window when they correlate, like the commands executed 
  in one can be monitored in other panes on the window.  Use separate windows for more unrelated concerns.
- Log monitoring: tail -f, kubectl log, stern
- Resource monitoring: top, kubectl –watch, lsof, netstat, ping,
- Re-running commands: watch.  Combine with cloud cli commands, database queries, metrics CLI commands, API queries,
  curl GET output, etc. and it could be more powerful.

### Analyzing Structured Logs Locally

You have a stream of structured logs in json, like from an application.  You want to be able to use them for troubleshooting, or for
analytics.  Each log record is separated by new lines.  You  might be surprised to hear, jq operates on space delimited records by default, 
it’s just that usually you are only giving it one record.  So you can use jq with streaming log data out of the box.  You may prefer local 
operations because the cloud logging solutions can be more expensive.  You can sink logs to a cheaper storage solution, and just 
download/query them when needed.  You can simply tail -f a log file and pipe it to jq.

### Using Brew

Brew is a package manager for Mac.  It is written in Ruby, and leverages github to retrieve formulae.

- Usually installed to its "prefix" /opt/homebrew
- Your PATH should contain "/opt/homebrew/bin"
- Brew will maintain symbolic links in "/opt/homebrew/bin" that point to specific versions of installed executables.
- Its object model splits resources into those that are built from source vs. pre-compiled binaries:
    - Built from source, called formulas:
        - A Cellar contains racks.  Cellar is a directory on your local machine, under the brew prefix directory, and it is maintained by brew.
        - A rack contains kegs and bottles.  A rack is associated with one formula, which is like one unversioned package or item of software.
        - A keg is a single version or release of software or a package.  Use `brew list` to see all installed kegs or formulae.
        - A bottle is a pre-built keg.  Why this is not called a cask, I have no idea.
        - A formula is a package definition, that gets built from some upstream source.
    - Pre-built binaries, called casks:
        - A Caskroom contains casks.  Caskroom is a directory on your local machine, under the brew prefix directory, 
          and it is maintained by brew.
        - A cask is a package definition that installs pre-compiled binaries. Use `brew casks` to see all installed casks.
        - I have not seen any mention of versions of a cask.
        - Strangely, there is not a separate term for a cask that is installed locally vs. the definition of a cask in a tap.
    - A tap is a directory that contains formulae.  Unlike a Cellar, this is not local.  This usually refers to a github repository that 
        brew will search for formula.  It is a source of packages that may not be installed locally.
        - Use `brew tap` to add a tap.  You can either provide the full URL, or just the path (it will assume github is the domain).
          So often times you can just omit the "https://github.com/" prefix to the URL.
        - There is an implicit tap to `https://github.com/Homebrew/homebrew-core`, it will not be listed in `brew taps` because it
          is used by default.
- A formula is more like an idea or specification for a package that is maintained externally from your local system, by a tap.
- A keg (built locally), bottle (pre-built), or cask (pre-built binary) refers to your local installation of a package.
- See dependencies of a keg or a cask with `brew deps [keg|cask]`
- `brew fetch` just downloads a keg or cask without linking to it.
- `brew link` just links the keg or cask into `/opt/homebrew/bin` without downloading it.  You can use this to revert a version of a package.
- `brew install` downloads the latest formula of a package into a keg, or the latest cask, 
  and automatically links it into `/opt/homebrew/bin`.  It is equivalent to fetching and then linking.
- `brew update` will update brew itself, and all formulae to their latest version.  
  `brew update` cannot be given a formula/keg/cask.
- `brew upgrade` can be given a formula/keg/cask to update, but that is not required.  
  Without a formula, it upgrades all outdated formulae.  It will not update brew itself.
- Troubleshooting:
    - Install a specific version of a formula: `brew install <formula@version>`
    - Revert a package: Use `brew link`
    - What versions of a package is active locally: `brew info <formula>`
    - List the versions of all installed packages locally: `brew list --versions`
    - What versions of a package exist externally? `brew search <formula>`
    - See what formula exist locally: `brew list`
    - See what formula exist on taps: `brew search`
    - See your taps: `brew tap`
    - Remove a command from PATH but not Cellar/Caskroom: `brew unlink`
    - get a description of an installed keg/formula/cask: `brew desc <formula>`
    - describe a formula that is on tap: There is no good command for this, you should review its description on the tap formula list,
      e.g. on [https://formulae.brew.sh/formula/](https://formulae.brew.sh/formula/)

## Top

On a mac, by default it sorts by pid which is probably not all that useful. 

- Change its order with `-o`, and specify descending with a "-" before the next arg.
    - Use `-o -mem` or `-o -cpu`
- The default delay is one second, change it with `-s <seconds>`
- The number of rows printed is infinite, you probably should limit it with `-n <nprocs>`
- Control what columns are shown with the `-stats` arg, which is a comma-delimited list of column names.

## Nohup

**Nohup:** Stands for “no hangup”, meaning a process will not stop if it receives a certain signal, like SIGHUP.  When a terminal is stopped 
or closed, that would send a SIGHUP signal to all of its child processes.  When using nohup, if the parent process stops, the child 
process will keep running.  Usually the parent process is a bash shell or a terminal that you may want to close or disconnect from.

## ZProfile vs ZRC

ZProfile is run everytime a bash shell starts.  ZRC is only run for interactive shells, like in a terminal.

## Using vim

- Use “:q” to quit, “:w” to write, “:x” to write and quit.
- Use “i” to insert, “A” to append to the end of the line, “o” to start writing on the following line.  Use escape to stop writing.
- Copy a line: yy (yank)
- Paste: p
- Delete or cut a line: dd
- Use “v” to start highlighting.

## Bash

- If statements
- switch
- For loops
- Single-quote strings are not interpreted, double quotes are.
- Comparisons, tests
- Arrays
- Configuration with set
- Bash versions
- “((...))” evaluates an arithmetic expression.  Exit code is zero if the result is non-zero.
- “[ ]” is equivalent to running test.  Must use arguments for comparison, not operators.  
- “[[  ]]” evaluates a condition, you can use operators like “==” or “!=”, “<”, “>”, etc. here.
- Use “=~” for regular expression matching.
- “!” negates an expression, changing its exit code to 1 if it was zero, or zero otherwise.
- “$?” returns the exit code of the last command.
- “&” at the end of a command means to run it asynchronously, in the background.
- Given “/foo/bar”, dirname is “/foo”, and basename is “bar”.  The base of a file is the last portion of it, its name.  
  The base of a directory is the last directory’s name.
- Lists:
    - “&&” if previous command succeeds (exit code 0), then run the next one, return its exit code.
    - “||”  if previous command fails (returns a non-zero exit code), then run the next one, return its exit code.
- Parameter expansion:
    - ${var:-default} - is “default” if var is blank
    - ${parameter:offset:length} - selects a substring, length is optional
    - ${#parameter} - the length of parameter’s value.
    - ${parameter#word} - removes the shortest prefix of parameter matching word.
    - ${parameter##word} - removes the longest prefix of parameter matching word.
    - ${parameter%word} - removes the shortest suffix of parameter matching word.
    - ${parameter%%word} - removes the longest suffix of parameter matching word.
    - ${parameter/pattern/string} - replaces first occurrence of pattern with the string.
- Redirection:
    - “>” write standard output to file, overwriting existing content
    - “>>” append standard output to the file, keeping existing content
    - “<” read file to standard input
    - “|” pass standard output of previous command to standard input of next, each command runs in its own subshell.
    - “|&” pass both standard input and error to the next command.
    - “&>” both standard error and output go to the file.
    - “<<<” the string after this becomes standard input to the command before.
    - “2>&1” - send standard error to the same place standard output goes.
- Named Pipes:
    - Act like the anonymous “|” operation, except they have files on the filesystem.
    - Actually keeps data in memory.
    - Separate processes can read/write to/from them.
    - File still exists after reboot, but is an empty buffer.
    - You can use “>” to write output to it, and “<” to read from it, just like any file.
    - Pipes are blocking, if a process writes to a pipe (named or not), 
      the kernel will block that process from completing until the data has been read.
    - Usually named pipes are not necessary, but they could be if there are multiple processes writing to or reading from a single pipe.
- Commands:
    - Tr: translate, use “-d” to delete a character.

### Posix Compliance

If you are writing a bash script that may run in a container, then you may want to only use features that are
defined in POSIX 2 shell standards.  The reason it matters is: if you change the underlying base image, the 
shell script may stop working because certain features it uses don't exist.

## Awk

- Programs are a list of rules like “pattern { actions }”, braces are literal.  
  Can separate them with new lines or semicolons.  If one rule matches, subsequent rules are still run, 
  unless the “next” keyword is used in the action.
- Special patterns BEGIN and END are supported.
- Regular expression patterns should be surrounded by “/”, like “/pattern/”
- “$0” refers to the whole line, “$1” the first item based on the delimiter, etc.
- “$NF” refers to the last field in a line.
- “Print” is a supported action.
- Omit pattern to have action run for every line.
- Omit action to have it print all lines that match the pattern, like grep.
- Patterns can also be logical conditions, evaluating to true/false.
- “~” comparison is supported, it means matches pattern.  “X ~ /[abc]/” for example.
- Supports if, for, while, do
- Variables can be assigned to, read back later, using equals.
- Can assign variables on the command line with “-v key=value”
- Fields are separated by white space by default, change it with “-F regexp”.
- Most useful env vars: FS=field separator, OFS=output field separator, RS=record separator (defaults to new line), 
  ORS=output record separator, NF=number of fields in current match, NR=number of the record in the file.
- If multiple arguments follow “print”, they are concatenated if there is no comma between them, 
  or delimited by ORS if there is a comma between them.
- Useful functions: length, toupper, tolower, split, sprintf(fmt, expr), and system (executes a command)

## Sed

- Replaces text by pattern matching.  Use “-e” to pass the expression.  Can pass multiple expressions, 
  use “-e” multiple times, or separate expressions with new lines.
- “s/pattern/replacement/” substitutes the pattern with the replacement
- Add “/g” to the end to make it global.
- End with “/d” to make it delete the line.
- Use “-i” to modify file in place, “-f” to pass a script file, “-e” to pass expression.  
  Use “-r” for extended regexp.  With no flags, the first string is the script, and the rest are files.
- Can pass multiple files to it.
- In non-extended regexp, some characters are not given their expected treatment, 
  the following are treated like plain characters: “?”, “+”, parentheses, braces  (squirrel brackets), and pipes.
- Syntax: “[addr]X[options]”, X is a character representing a command.
- Addr can be a line number, a regex (enclosed in forward slashes), or a range of lines, delimited by a comma.  Omit to select all lines.
- Can delimit multiple commands with semicolons, with exceptions.
- Favorite commands: a=append, c=change, d=delete, i=insert before, s=substitute, q=quit with exit code
- Substitution flags: g=global, i=insensitive to case.
- Supports a “hold space”, but it’s more complicated than it’s worth.
- On a mac, sed "-i" takes an argument, specifying a backup file path to copy the original file to.  You can use '*' in the
  argument as the original file base name.  On linux, "-i" does not take an argument and does not create a backup file.

## Grep

- Outputs lines of a file or standard input that match an expression.
- Uses basic regular expressions unless you use “-E”
- Add “-H” to print the file a line came from first.
- Add “-n” to print the line number the match came from.
- Add “-A num” to print a number of lines after the match too.
- Add “-B num” to print a number of lines before the match too.
- “-C num” is like a combination of “-A” and “-B”, it prints a number of lines before and after each match.
- Use “-c” to just print the count of matches.
- Use “-R” to recurse files in a directory, you can exclude files with “--exclude”
- The exit code is 1 (false) if no matches were found, which makes it easy to use grep in if statements.

## Date

- prints the current date and time in your time zone.  Add “-u” for UTC
- If you pass a positional argument not starting with “+”, it attempts to set the date and time.  Use “-j” to prevent that, 
  which enables you to convert one date format to another.  To convert, use “-f” to specify the input format, 
  example: date -j -f <input_format> <input_date> “+<output_format>”
- If you pass a positional argument starting with “+”, you can specify a format for the date to be printed.  
  You use a “%” symbol to indicate a time - field.  Y=year, m=month, d=day, H=hour, M=minute, S=second, s=epoch seconds.  
  Run “man strftime” to see more format options.
- To print the epoch time: date “+%s”
- If you have an epoch time, convert it with “-r”: date -r <epoch_seconds>
- Offset a time using the “-v” argument, then +/- <number> <units>

## Understanding /etc/hosts

- In the file, lines are like `<IP address> <hostname>[,<hostname>...]`.
- It can also have "#" comments.
- Editing it requires root, privileged, or superuser privileges.  At work you usually will not have this.

Also if you edit the hosts file and depend on that in tests, your work will not easily be 
transferrable to co-workers.  Docker and Kubernetes manage the /etc/hosts files for you, so you 
should not change it in container images.  It's generally best to avoid messing with it.
If the file is modifiable, co-workers could easily prank you if you leave your machine unlocked,
by re-directing some common domain to something else nefarious.

Some realistic uses for it are local testing, blocking bad websites, and bypassing a DNS cache update.
Since DNS caches results of lookup for a TTL, you will need to wait for that cache to expire before you 
see updates, unless you modify the /etc/hosts file.

## Sockets vs. Ports

Ports are a number that uniquely identifies a process or service running on a host machine.  They are stateless and use almost no resources.
Sockets combine a port with an IP address.  They enable bi-directional communication.  They are stateful, and use file descriptors.

## Graceful Shutdown

Usually done by sending a SIGTERM first, waiting a little while, then sending a SIGKILL if the process is still running.  
This gives the process a little time to perform cleanup actions.  K8s follows this pattern.

## Unfinished Documentation

- listing processes: ps -aux
- listing ports: netstat
- sysctl: just manages a bunch of variables for the kernal, use `sysctl -a`
- launchctl: manages what services and processes are installed and run as daemons.  
    - Some useful sub-commands: list, enable, disable, start, stop, kill, and print

Next: understanding python search paths, imports, and virtual environments.
Understanding system daemons, and controlling them on mac/linux.
Enabling software on a mac.
os security: sssd


Converting Confluence to Markdown and vice versa.
posix, command line styles.  Sometimes short args can be combined.  Sometimes an "=" can be used, sometimes not.  
Sometimes a space is needed,
  sometimes not.

Curl
Openssl
Find
Time: is for timing the run of some other process

Mounting Drives

Formatting Filesystems

Passwordless Sudo

understanding active directory

Kerberos: Both authentication and authorization.

SAML: XML based authentication similar to oauth.  Legacy systems use it.

OAuth2: json based authentication
OIDC: open identity connect, json based authentication.

Samba: Is software that runs on an Ubuntu server which enables Windows environments to inter-operate with it, such as by sharing files and/or
  network resources.  You can share printers, authentication/access, active directory membership, etc.

Makefile
