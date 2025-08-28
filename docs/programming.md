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

# Language Classification

- Declarative: states what you want, not how to achieve it.
- Imperative: states how to reach the goal.
- Statically Typed: variables have strict types.
- Functional: Collections have operations to pipe them through to a result.
- Compiled: converts into machine code.
- Scripting: interpreted, at runtime.
- Turing Complete: Has enough functionality to do anything.

# Python

- Use four spaces to indent lines, use colons to start blocks.  Squirrel brackets are not used for code blocks.
- No ternary
- Bools are capitalized True or False
- Iterate over a range: for i in range(start, end, interval), start and interval are optional, end is exclusive.
- Iterate with an index using enumerate.  Example: “for index, value in enumerate(list):”
- Lists are “[]”, dictionaries/maps are “{key: value}”, sets also use {}, but no values.  To make an empty set you must use “set()”
- Use yield in a function to make it a generator, and it can be used in for loops.
- To add type annotations, import typing.  These are called annotations.
- Adding types to arguments or return values are called annotations.
- Select the last item in an index with list[-1]
- Sub select a list with list[start:end], end is exclusive, can also specify an interval, can omit start or end.
- Lambdas can only have one expression: “lambda x, y: x+y”  There is an implicit return.
- Get ascii integer from string with ord, and chr is the reverse.
- No switch statement, use “elif” in sequences.  There is a match statement, which unpacks values into variables, it does type matching as well.
- Use ** to calculate exponents.  Auto-converts integers to floats.
- Can use single quotes to define strings.  Add “r” prefix to avoid string interpretation.
- Repeat strings with “*”.  Can use triple quotes for multi-line strings.  Use “f” prefix to embed format expressions.
- For loop doesn’t support init, check, iterate format, use for … in … format.  Use range function for a sequence of integers.  
  Convert iterator/sequence to list with list(iterator), not [iterator], which would make a list with one thing, the iterator.
- Use while with a condition.  Can use a variable as condition, which is truthy if it’s not None, a boolean true, or a non-zero number.  Loops support - continue/break.
- Pass statement does nothing.  It’s a placeholder.
- Match statement does pattern matching on types, can unpack types.  Useful if inputs can be different types.  
  Combine cases with “|”.  Use “_” to match all.
- String methods: join, split, replace.  Check contains using “in” operator.
- List methods: append.  Shallow copy using “[:]”.  Check length with len operator.
- Map methods: copy, items (key, value).  Use del operator to remove entries.  Normal iteration over a map just yields the keys, in order they were - added.  Using the items function iterates over the tuples of keys and values.
- Print command accepts an “end=” argument, to not end with a new line.
- Can specify named arguments to functions instead of positional.  Can define default values for a function.  Once you set defaults, all remaining - arguments must have defaults.  Once you provide a named argument, all remaining arguments must be named.  If a default value is an empty list, that - gets re-used on sub-sequent calls, which is probably not what you wanted.  Use None instead.  Can use “arguments” for all unexpected positional - arguments, or “*keywords” for all unexpected named arguments.  Python tends to preserve order even in maps and sets.
- Supports tuples with “()” parentheses.  They are immutable sequences.
- Add docstrings to functions as a string inside the function, not assigned to a variable.
- Most things are mutable.  If you define a class and want fields to be private, prefix them with underscores, which doesn’t really make them private, it just discourages people from using them.  Fields can be added to objects any time by just assigning them, Python is dynamic.

## Command Line Arguments

- `python -c "script ..."` runs an inline program
- `python -m <module>` runs an installed module as an executable, use `pip list` to see what modules are known.
- "-h" for help, "-v" for version.
- `python <file>` runs the file with `__name__` set to `__main__`.
- options "-E", "-I", "-P", and "-s" can be used to isolate python from the environment, use them with caution as the import
  search path may not find things you expect it to.

## Python Dependencies

- A [module](https://docs.python.org/3/tutorial/modules.html) is a file containing python code (definitions and statements).  
  The file name without its suffix is the module name.
- The module name is stored as the `__name__` variable.
- If a module is invoked directly from the python command line, then its `__name__` will be set to `__main__`, so we often
  have `if __name__ == "__main__":` to make a module executable.
- If you import a module by its name, the importing file can refer to any of the imported definitions by prefixing them
  with the module name and a period.  If you `import foo` then you can invoke `foo.bar()`
- When you import a module, it basically executes the whole module.  Anything it defines will continue to exist in that module's
  namespace.  Consequently, if the module executes statements, then merely importing it can have side affects.
- If you want to import a definition from a module directly into the current namespace, use the form `from <module> import <def>[, <def2>...]`
  For example, if you `from foo import bar` then your code can invoke `bar()`.
- You can use the keyword `as` to rename modules and/or their definitions while importing them.
- A package may contain modules and sub-packages.
- Think of python files as being "modules", and directories with python code as "packages".
- All packages are modules, but not all modules are packages.
- A package is a module that also has a `__path__` attribute.  Therefore, if you set `__path__` in a python file, you have created a module 
  that is also a package.
- When a package is imported, the `__init__.py` file is automatically executed.
- If you import a sub-package, all parents/ancestor packages are also automatically initialized, before the child package.
- Module search paths:
  - First searches for built in modules with the given name.  This cannot be changed, overridden, or hidden.
  - Then considers:
    - the current working directory if it was run without the "-m" argument.
    - the directory holding the module being invoked with `python -m <module>`.  Use `pip list` to see what modules or packages are installed.
  - Then it searches any directory found in the PYTHONPATH environment variable.
  - It may consider some directories defined by the python installation, i.e. site-packages in the python home directory.
  - All paths searched after built-in module paths are captured in the "sys.path" variable, which can actually be modified
    after the module has started executing.  I would avoid modifying it, to keep things simple.
  - If you were to name a module the same as a standard library module, it would replace it, because the local directory is 
    searched before the standard library modules.  So you should probably avoid naming modules the same as python standard modules.
  - Therefore, imports of your own code should probably be relative to whatever root module's directory that you expect to run.
    So if you keep your main module at the root of a project, and it has the command line parsing code and sub-commands, you
    should define all imports relative to the directory holding that main module.
    - If you were to invoke a sub-module in a sub-directory, the import path would be mis-aligned, and you should probably 
      correct it by setting PYTHONPATH.
- Python caches compiled modules and re-uses them if the source has not changed since the file was compiled.  This will
  not alter the long term runtime or request processing time, it will only affect how fast it boots or loads modules.
- Usually an import statement includes the very file or module being imported, not just the directory/package holding it.
- If you import a module within a package, you still need to refer to it with its full path.  if you `import foo.bar`,
  then running `bar.baz()` will not work, you must run `foo.bar.baz()` instead.  Avoid this with `from foo import bar`, then
  `bar.baz()` will work.
- In `__init__.py` if you define `__all__` to be a list of strings, those items are the module names to be imported by "*".
  If somebody imports "*" from a package and `__all__` is not defined, it will only run the `__init__.py` file, and sub-modules
  will not be imported.

- requirements.txt
- python command line arguments.  python -m
- where these things exist.  What modules are available.

### venv

Environment management only.  Native to python, comes out of the box, even if it's not listed in the installed modules by `pip list`.  
brew can override pip, blocking it from running, and insisting that you use brew to manage python packages instead.

- create an environment: `python -m venv <name>`.  Name is really a relative path.  Usually you prefix the name with `.venv/` so it's hidden.
  - note that "venv" can be used even if python does not list it with `pip list`.
- activate it: `source [.venv/]<name>/bin/activate`
  - this essentially works by prefixing your PATH environment variable with the virtual environment's bin directory.
  - pip and python are overridden to use that of your virtual environment.
  - running pip should be equivalent to running `python -m pip`.  Usually pip is aliased to pip3, and python is aliased to python3.
  - Typically your next step would be `pip install -r requirements.txt`
  - Activating this environment only affects the terminal you activated it in.
- exit the virtual environment: `deactivate`
- You can delete the whole directory for the virtual environment if you no longer need it.
- It's not safe to copy, move, or rename a virtual environment.  It won't work.  Just make a new one and set it up using the requirements file.
- venv is simpler to use than most alternative virtual environments.
- Virtual environments contain the entire Python interpreter, which is large, so you should not commit virtual environments into 
  source control, you should only commit the requirements.txt file and expect co-workers to setup their own virtual environments from that.
  Specifically, you should add `.venv/` to `.gitignore`, assuming you store those environments in that directory.

### Pip

Dependency management only.  `pip` should be interchangeable with `python -m pip`.
- `pip list` will list installed packages.
- `pip show <package>` shows information about a package.
- `pip install <package>` installs the package, usually searches a common python repo.
- `pip uninstall <package>` removes it.
- To upgrade a package: `pip install --upgrade <package>`
- To use a specific package version: `pip install <package>==<version>`
- Save dependencies to a file: `pip freeze > requirements.txt`
- Load dependencies from a file: `pip install -r requirements.txt`
- Usually the requirements file is committed to source control for others.

### VirtualEnv

Environment management only.  Predates venv, works for python2 and older versions of python3.  Has more features and is more complicated.
Is a third-party module, you must install it.  You can tell it to not inherit site packages from the global python installation.
That could also be accomplished by running in a container.
Typically this is not used for Python 3.3 or later.

### Poetry

Both environment and dependency management.

### Conda

Both environment and dependency management.  Slower to update.
Not just for python.  Does ruby, R, and Node as well.

## Ansible

## Argo Workflows

# Golang

Functions
If
For (while)
Switch
Select
Goroutines
Channels

# Regular Expressions

“^” means start of line.  “$” means end of line.  Most characters match themselves.
“.” means any character.
“*” means repeated zero, one, or many times.
“+” means repeated one or more times.
“?” means zero or one times.
Parentheses enclose groups, which can be used later in back references.  You can follow groups with expressions specifying their required occurrence or frequency.
“|” means the pattern on the left or right may match.
Braces indicate a number of times something must match.  “{n}” means it must occur n times, “{n, m}” means it must occur between n and m times.
Square brackets define a set of characters that are allowed.
Start or end with a hyphen to say that hyphens are allowed.  Otherwise…
Hyphens are used to define ranges of characters, like a-z
Start with a “^” to negate the pattern.
Flags can be provided like “(?flags)” or “(?flags:regex)”.  Flags:
“i” insensitive to case
“m” multi line.

# DotNet

.Net is an open source set of software developed by Microsoft for software development.  C# (C-sharp) is its primary programming language, and is compiled and statically typed.  Nuget is its package manager.  Abstract server pages (ASP) is its web framework.  It can run on other operating systems like Linux and MacOS.  F# (F-sharp) is their scripting language.  Microsoft also provides Azure as its cloud, and SQL Server as its relational database.  These are not strictly used with .Net.  They also provide Visual Studio and VSCode for IDEs.

# Jinja2

Templates, text rendering engine, for Python.

- Use double braces for expressions, e.g. to insert values from variables.
- Use “{#  #}” for comments
- Use “{%  %}” for directives, like adding loops or other control structures.
- For … endfor, supports ranges.
- If, elif, else, endif
- Filters are functions that expressions can pipe data into.  Like “{{ expression | filter }}”, 
  the output of expression will be the argument for filter, and the output of the filter gets inserted into the text.
- It will escape HTML content by default after evaluating expressions.

# Javascript