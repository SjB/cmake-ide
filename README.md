cmake-ide
==========

[![Build Status](https://travis-ci.org/atilaneves/cmake-ide.svg?branch=master)](https://travis-ci.org/atilaneves/cmake-ide)

[CppCon 2015 Lighning talk on cmake-ide](https://www.youtube.com/watch?v=5FQwQ0QWBTU).

`cmake-ide` is a package to enable IDE-like features on Emacs for
[CMake](http://www.cmake.org/) projects. This includes autocompletion
and on-the-fly syntax checking in Emacs for CMake projects with
minimal configuration. It uses other packages to do its heavy lifting,
in a combination of:

* [rtags](https://github.com/Andersbakken/rtags)
* [flycheck](https://github.com/flycheck/flycheck)
* [auto-complete-clang](https://github.com/brianjcj/auto-complete-clang)
* [company-clang](https://github.com/company-mode/company-mode/blob/master/company-clang.el)
* [irony](https://github.com/Sarcasm/irony-mode)

`cmake-ide` will set variables and call functions for the installed
dependent packages.

It works by running CMake in Emacs in order to obtain the necessary
`-I` and `-D` compiler flags to pass to the other tools. Since all
the dependencies are specified in the CMake scripts, there is no
need to maintain a parallel dependency tracking system for Emacs.
Just ask CMake.

Features
--------
* Sets variables for `auto-complete-clang`, `flycheck` and others for a CMake
  project automagically. Hardly any configuration necessary.
* Automatically reruns CMake when a file is saved. Great when using
CMake file globs to pick up newly created files, but needs
`cmake-ide-dir` to be set.
* `cmake-ide-delete-file` allows you to have the same convenience when
deleting files. I can't figure out a better way to do this. Obviously
simply deleting the file means having to run CMake again manually for
it to register the change in the list of files to be compiled.
* If `cmake-ide-dir` is set, it is considered to be the build
directory to run CMake in. Additionally, this will cause
`cmake-ide-compile` to compile the project there. It automatically
detects Ninja and Make builds and sets the compile command
accordingly. The command to use can be customised by setting
the `cmake-compile-command` variable.
* `cmake-ide` can make usage of
[rtags](https://github.com/Andersbakken/rtags) for finding
definitions, also using clang. If `(require 'rtags)` is called before
`cmake-ide-setup`, it will automatically start the rtags server (`rdm`)
and call `rc -J` to index the project files for 0-config "jump to
definition" and everything else rtags offers.  This only works if both
`rdm` and `rc` and in the system path or if `cmake-ide-rdm-executable`
and `cmake-ide-rc-executable` are customized correctly.

Installation
------------

Install from [MELPA](https://melpa.org) or [MELPA Stable](https://stable.melpa.org/) with:

    M-x package-install RET cmake-ide.


Usage
-----

Add this to your `.emacs` / `init.el`:

    (require 'rtags) ;; optional, must have rtags installed
    (cmake-ide-setup)

If `cmake-ide-clang-flags-c` or `cmake-ide-flags-c++` are set, they
will be added to `ac-clang-flags`.  These variables should be
set. Particularly, they should contain the system include paths
(e.g. `'("-I/usr/include/c++/4.9.1" "...")`. For a system with gcc,
you can get this information by running `gcc -v -xc++ /dev/null
-fsyntax-only` (it's the same prerequisite for `auto-complete-clang`
to work, since that's how clang itself works).

And... that's it. It works by calling cmake and parsing the resulting
JSON file with compiler flags.  Set `cmake-ide-dir` to your project's
root directory and you won't have to call CMake manually again (except
for the first time to specify options). Best done with
[directory local variables](https://www.gnu.org/software/emacs/manual/html_node/emacs/Directory-Variables.html).


Related Projects:
----------------
* [cpputils-cmake](https://github.com/redguardtoo/cpputils-cmake).
* [My CMake modules for emacs. cmake-ide is definitely better](https://github.com/atilaneves/cmake_modules).
