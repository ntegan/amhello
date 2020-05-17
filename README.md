# automake hello world
https://www.gnu.org/software/automake/manual/automake.html#Hello-World

## demo package for GNU Automake
```
info Automake
```

to read the automake manual

## Usage
```
autoreconf --install
./configure
make
./src/hello # to run the executable
```

## distributing automake software
To distribute software for build from source,
first run the `autoreconf --install` which will initialize the GNU Build system.

then a user should `./configure`, `make`, `make check`, `su`, `make install`,
`make installcheck`.

## Info
autoreconf creates `configure`, `config.h.in`, `Makefile.in`, `src/Makefile.in`

the latter three files are templates that will be adapted to system
by `configure` under names `config.h`, `Makefile`, and `src/Makefile`

`autoreconf` calls `autoconf`, `automake` and other commands in a certain order

`autoconf` creates `configure` from `configure.ac`,
`automake` creates `Makefile.in`s from `Makefile.am`s and `configure.ac`


## `configure.ac` explanation
`configure.ac` is read by `autoconf` and `automake`.
It contains M4 macros that will be expanded to form `configure` script.
(see "Writing `configure.ac`" in the Autoconf Manual)

`AC_*` are autoconf macros (see autoconf macro index),
and `AM_*` are automake macros (see automake macro index)

argument to `AM_INIT_AUTOMAKE` is list of options for `automake` 
(see options in manual).
`-Wall and -Werror` turns on all Automake warnings and reports as errors.
(e.g. incorrect instructions in `Makefile.am`).
`foreign` tells Automake that this package will not follow GNU Standards
(e.g. ChangeLog, AUTHORS, etc.)

`AC_PROG_CC` causes `configure` script to search for a C compiler, and define
the `CC` variable with its name.

`src/Makefile.in` gen'd by Automake uses this `CC` var to build `hello`,
so when `configure` creates `src/Makefile` from `src/Makefile.in` 
it will define CC with val it has found.

`AC_CONFIG_HEADERS([config.h])` causes `configure` to create `config.h`
which gathers `#define`'s defined by other macros in `configure.ac`
(e.g. `PACKAGE_BUGREPORT` and `PACKAGE_STRING`).
In an actual project, `config.h` can contain one define per feature probed on system.

`AC_CONFIG_FILES` declares list of files that `configure` should create from
`*.in` templates.
Automake also scans this list to find `Makefile.am` files it must process.
(important) when adding new directory to proj, add its `Makefile` to this list.
otherwise Automake will not process the new `Makefile.am` you wrote in that dir.

Finally, `AC_OUTPUT` is a closing command that produces part of script in charge
of creating files registerd with `AC_CONFIG_HEADERS` and `AC_CONFIG_FILES`

When starting a new project, start with simple `configure.ac` and 
gradually add other tests(?) it requires.
`autoscan` can suggest a few tests that package may need.
(see Using `autoscan` to create `configure.ac` in autoconf man)



## `Makefile.am` explanation

#### `src/Makefile.am`

has same syntax as ordinary makefile.

`*_PROGRAMS` suffix to variables is called a primary.
other primaries include `_SCRIPTS`, `_DATA`, `_LIBRARIES`...
`bin` in `bin_PROGRAMAS` means hello should be installed in the `bindir`

`hello.c` will be included in a tarball created by `make dist`.


#### `Makefile.am`
`SUBDIRS` lists directories that `make` should recurse into before process
current dir.
it also causes `src/hello` to be installed with `make install` before
`README.md`

`dist_doc_DATA` causes the readme to be distributed and installed in `docdir`


Not covered here is accessing install directory valies (See standard dir variables)
from program code. i.e. converting them into defined macros 
(Defining directories) in automake manual

