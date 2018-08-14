---
layout: post
category: ""
title:  "CUN Configuration"
tags: [Configuration]
---

**This is a reprint.**
**Copy from [the Page](https://airs.com/ian/configure/configure_2.html).**


To start using the GNU configure and build system with your software package, you must write three files, and you must run some tools to manually generate additional files.

Write configure.in

You must first write the file `configure.in'. This is an autoconf input file, and the autoconf manual describes in detail what this file should look like.

You will write tests in your `configure.in' file to check for conditions that may change from one system to another, such as the presence of particular header files or functions.

For example, not all systems support the `gettimeofday' function. If you want to use the `gettimeofday' function when it is available, and to use some other function when it is not, you would check for this by putting `AC_CHECK_FUNCS(gettimeofday)' in `configure.in'.

When the configure script is run at build time, this will arrange to define the preprocessor macro `HAVE_GETTIMEOFDAY' to the value 1 if the `gettimeofday' function is available, and to not define the macro at all if the function is not available. Your code can then use `#ifdef' to test whether it is safe to call `gettimeofday'.

If you have an existing body of code, the `autoscan' program may help identify potential portability problems, and hence configure tests that you will want to use. See the autoscan documentation.

Another handy tool for an existing body of code is `ifnames'. This will show you all the preprocessor conditionals that the code already uses. See the ifnames documentation.

Besides the portability tests which are specific to your particular package, every `configure.in' file should contain the following macros.

`AC_INIT'
This macro takes a single argument, which is the name of a file in your package. For example, `AC_INIT(foo.c)'.
`AC_PREREQ(VERSION)'
This macro is optional. It may be used to indicate the version of `autoconf' that you are using. This will prevent users from running an earlier version of `autoconf' and perhaps getting an invalid `configure' script. For example, `AC_PREREQ(2.12)'.
`AM_INIT_AUTOMAKE'
This macro takes two arguments: the name of the package, and a version number. For example, `AM_INIT_AUTOMAKE(foo, 1.0)'. (This macro is not needed if you are not using automake).
`AM_CONFIG_HEADER'
This macro names the header file which will hold the preprocessor macro definitions at run time. Normally this should be `config.h'. Your sources would then use `#include "config.h"' to include it. This macro may optionally name the input file for that header file; by default, this is `config.h.in', but that file name works poorly on DOS filesystems. Therefore, it is often better to name it explicitly as `config.in'. This is what you should normally put in `configure.in':
AM_CONFIG_HEADER(config.h:config.in)
(If you are not using automake, use `AC_CONFIG_HEADER' rather than `AM_CONFIG_HEADER').
`AM_MAINTAINER_MODE'
This macro always appears in Cygnus configure scripts. Other programs may or may not use it. If this macro is used, the `--enable-maintainer-mode' option is required to enable automatic rebuilding of generated files used by the configure system. This of course requires that developers be aware of, and use, that option. If this macro is not used, then the generated files will always be rebuilt automatically. This will cause problems if the wrong versions of autoconf, automake, or others are in the builder's `PATH'. (If you are not using automake, you do not need to use this macro).
`AC_EXEEXT'
Either this macro or `AM_EXEEXT' always appears in Cygnus configure files. Other programs may or may not use one of them. This macro looks for the executable suffix used on the host system. On Unix systems, this is the empty string. On Windows systems, this is `.exe'. This macro directs automake to use the executable suffix as appropriate when creating programs. This macro does not take any arguments. The `AC_EXEEXT' form is new, and is part of a Cygnus patch to autoconf to support compiling with Visual C++. Older programs use `AM_EXEEXT' instead. (Programs which do not use automake use neither `AC_EXEEXT' nor `AM_EXEEXT').
`AC_PROG_CC'
If you are writing C code, you will normally want to use this macro. It locates the C compiler to use. It does not take any arguments. However, if this `configure.in' file is for a library which is to be compiled by a cross compiler which may not fully work, then you will not want to use `AC_PROG_CC'. Instead, you will want to use a variant which does not call the macro `AC_PROG_CC_WORKS'. Examples can be found in various `configure.in' files for libraries that are compiled with cross compilers, such as libiberty or libgloss. This is essentially a bug in autoconf, and there will probably be a better workaround at some point.
`AC_PROG_CXX'
If you are writing C++ code, you will want to use this macro. It locates the C++ compiler to use. It does not take any arguments. The same cross compiler comments apply as for `AC_PROG_CC'.
`AM_PROG_LIBTOOL'
If you want to build libraries, and you want to permit them to be shared, or you want to link against libraries which were built using libtool, then you will need this macro. This macro is required in order to use libtool. By default, this will cause all libraries to be built as shared libraries. To prevent this--to change the default--use `AM_DISABLE_SHARED' before `AM_PROG_LIBTOOL'. The configure options `--enable-shared' and `--disable-shared' may be used to override the default at build time.
`AC_DEFINE(_GNU_SOURCE)'
GNU packages should normally include this line before any other feature tests. This defines the macro `_GNU_SOURCE' when compiling, which directs the libc header files to provide the standard GNU system interfaces including all GNU extensions. If this macro is not defined, certain GNU extensions may not be available.
`AC_OUTPUT'
This macro takes a list of file names which the configure process should produce. This is normally a list of one or more `Makefile' files in different directories. If your package lives entirely in a single directory, you would use simply `AC_OUTPUT(Makefile)'. If you also have, for example, a `lib' subdirectory, you would use `AC_OUTPUT(Makefile lib/Makefile)'.
If you want to use locally defined macros in your `configure.in' file, then you will need to write a `acinclude.m4' file which defines them (if not using automake, this file is called `aclocal.m4'). Alternatively, you can put separate macros in an `m4' subdirectory, and put `ACLOCAL_AMFLAGS = -I m4' in your `Makefile.am' file so that the `aclocal' program will be able to find them.

The different macro prefixes indicate which tool defines the macro. Macros which start with `AC_' are part of autoconf. Macros which start with `AM_' are provided by automake or libtool.

Write Makefile.am

You must write the file `Makefile.am'. This is an automake input file, and the automake manual describes in detail what this file should look like.

The automake commands in `Makefile.am' mostly look like variable assignments in a `Makefile'. automake recognizes special variable names, and automatically add make rules to the output as needed.

There will be one `Makefile.am' file for each directory in your package. For each directory with subdirectories, the `Makefile.am' file should contain the line

SUBDIRS = dir dir ...
where each dir is the name of a subdirectory.

For each `Makefile.am', there should be a corresponding `Makefile' in the `AC_OUTPUT' macro in `configure.in'.

Every `Makefile.am' written at Cygnus should contain the line

AUTOMAKE_OPTIONS = cygnus
This puts automake into Cygnus mode. See the automake manual for details.

You may to include the version number of `automake' that you are using on the `AUTOMAKE_OPTIONS' line. For example,

AUTOMAKE_OPTIONS = cygnus 1.3
This will prevent users from running an earlier version of `automake' and perhaps getting an invalid `Makefile.in'.

If your package builds a program, then in the directory where that program is built you will normally want a line like

bin_PROGRAMS = program
where program is the name of the program. You will then want a line like

program_SOURCES = file file ...
where each file is the name of a source file to link into the program (e.g., `foo.c').

If your package builds a library, and you do not want the library to ever be built as a shared library, then in the directory where that library is built you will normally want a line like

lib_LIBRARIES = libname.a
where `libname.a' is the name of the library. You will then want a line like

libname_a_SOURCES = file file ...
where each file is the name of a source file to add to the library.

If your package builds a library, and you want to permit building the library as a shared library, then in the directory where that library is built you will normally want a line like

lib_LTLIBRARIES = libname.la
The use of `LTLIBRARIES', and the `.la' extension, indicate a library to be built using libtool. As usual, you will then want a line like

libname_la_SOURCES = file file ...
The strings `bin' and `lib' that appear above in `bin_PROGRAMS' and `lib_LIBRARIES' are not arbitrary. They refer to particular directories, which may be set by the `--bindir' and `--libdir' options to `configure'. If those options are not used, the default values are based on the `--prefix' or `--exec-prefix' options to `configure'. It is possible to use other names if the program or library should be installed in some other directory.

The `Makefile.am' file may also contain almost anything that may appear in a normal `Makefile'. automake also supports many other special variables, as well as conditionals.

See the automake manual for more information.

Write acconfig.h

If you are generating a portability header file, (i.e., you are using `AM_CONFIG_HEADER' in `configure.in'), then you will have to write a `acconfig.h' file. It will have to contain the following lines.

/* Name of package.  */
#undef PACKAGE

/* Version of package.  */
#undef VERSION
This requirement is really a bug in the system, and the requirement may be eliminated at some later date.

The `acconfig.h' file will also similar comment and `#undef' lines for any unusual macros in the `configure.in' file, including any macro which appears in a `AC_DEFINE' macro.

In particular, if you are writing a GNU package and therefore include `AC_DEFINE(_GNU_SOURCE)' in `configure.in' as suggested above, you will need lines like this in `acconfig.h':

/* Enable GNU extensions.  */
#undef _GNU_SOURCE
Normally the `autoheader' program will inform you of any such requirements by printing an error message when it is run. However, if you do anything particular odd in your `configure.in' file, you will have to make sure that the right entries appear in `acconfig.h', since otherwise the results of the tests may not be available in the `config.h' file which your code will use.

(Thee `PACKAGE' and `VERSION' lines are not required if you are not using automake, and in that case you may not need a `acconfig.h' file at all).

Generate files
Once you have written `configure.in', `Makefile.am', `acconfig.h', and possibly `acinclude.m4', you must use autoconf and automake programs to produce the first versions of the generated files. This is done by executing the following sequence of commands.

aclocal
autoconf
autoheader
automake
The `aclocal' and `automake' commands are part of the automake package, and the `autoconf' and `autoheader' commands are part of the autoconf package.

If you are using a `m4' subdirectory for your macros, you will need to use the `-I m4' option when you run `aclocal'.

If you are not using the Cygnus tree, use the `-a' option when running `automake' command in order to copy the required support files into your source directory.

If you are using libtool, you must build and install the libtool package with the same `--prefix' and `--exec-prefix' options as you used with the autoconf and automake packages. You must do this before running any of the above commands. If you are not using the Cygnus tree, you will need to run the `libtoolize' program to copy the libtool support files into your directory.

Once you have managed to run these commands without getting any errors, you should create a new empty directory, and run the `configure' script which will have been created by `autoconf' with the `--enable-maintainer-mode' option. This will give you a set of Makefiles which will include rules to automatically rebuild all the generated files.

After doing that, whenever you have changed some of the input files and want to regenerated the other files, go to your object directory and run `make'. Doing this is more reliable than trying to rebuild the files manually, because there are complex order dependencies and it is easy to forget something.

Example
Let's consider a trivial example.

Suppose we want to write a simple version of `touch'. Our program, which we will call `poke', will take a single file name argument, and use the `utime' system call to set the modification and access times of the file to the current time. We want this program to be highly portable.

We'll first see what this looks like without using autoconf and automake, and then see what it looks like with them.

First Try
Here is our first try at `poke.c'. Note that we've written it without ANSI/ISO C prototypes, since we want it to be highly portable.

#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <utime.h>

int
main (argc, argv)
     int argc;
     char **argv;
{
  if (argc != 2)
    {
      fprintf (stderr, "Usage: poke file\n");
      exit (1);
    }

  if (utime (argv[1], NULL) < 0)
    {
      perror ("utime");
      exit (1);
    }

  exit (0);
}
We also write a simple `Makefile'.

CC = gcc
CFLAGS = -g -O2

all: poke

poke: poke.o
	$(CC) -o poke $(CFLAGS) $(LDFLAGS) poke.o
So far, so good.

Unfortunately, there are a few problems.

On older Unix systems derived from BSD 4.3, the `utime' system call does not accept a second argument of `NULL'. On those systems, we need to pass a pointer to `struct utimbuf' structure. Unfortunately, even older systems don't define that structure; on those systems, we need to pass an array of two `long' values.

The header file `stdlib.h' was invented by ANSI C, and older systems don't have a copy. We included it above to get a declaration of `exit'.

We can find some of these portability problems by running `autoscan', which will create a `configure.scan' file which we can use as a prototype for our `configure.in' file. I won't show the output, but it will notice the potential problems with `utime' and `stdlib.h'.

In our `Makefile', we don't provide any way to install the program. This doesn't matter much for such a simple example, but a real program will need an `install' target. For that matter, we will also want a `clean' target.

Second Try
Here is our second try at this program.

We modify `poke.c' to use preprocessor macros to control what features are available. (I've cheated a bit by using the same macro names which autoconf will use).

#include <stdio.h>

#ifdef STDC_HEADERS
#include <stdlib.h>
#endif

#include <sys/types.h>

#ifdef HAVE_UTIME_H
#include <utime.h>
#endif

#ifndef HAVE_UTIME_NULL

#include <time.h>

#ifndef HAVE_STRUCT_UTIMBUF

struct utimbuf
{
  long actime;
  long modtime;
};

#endif

static int
utime_now (file)
     char *file;
{
  struct utimbuf now;

  now.actime = now.modtime = time (NULL);
  return utime (file, &now);
}

#define utime(f, p) utime_now (f)

#endif /* HAVE_UTIME_NULL  */

int
main (argc, argv)
     int argc;
     char **argv;
{
  if (argc != 2)
    {
      fprintf (stderr, "Usage: poke file\n");
      exit (1);
    }

  if (utime (argv[1], NULL) < 0)
    {
      perror ("utime");
      exit (1);
    }

  exit (0);
}
Here is the associated `Makefile'. We've added support for the preprocessor flags we use. We've also added `install' and `clean' targets.

# Set this to your installation directory.
bindir = /usr/local/bin

# Uncomment this if you have the standard ANSI/ISO C header files.
# STDC_HDRS = -DSTDC_HEADERS

# Uncomment this if you have utime.h.
# UTIME_H = -DHAVE_UTIME_H

# Uncomment this if utime (FILE, NULL) works on your system.
# UTIME_NULL = -DHAVE_UTIME_NULL

# Uncomment this if struct utimbuf is defined in utime.h.
# UTIMBUF = -DHAVE_STRUCT_UTIMBUF

CC = gcc
CFLAGS = -g -O2

ALL_CFLAGS = $(STDC_HDRS) $(UTIME_H) $(UTIME_NULL) $(UTIMBUF) $(CFLAGS)

all: poke

poke: poke.o
	$(CC) -o poke $(ALL_CFLAGS) $(LDFLAGS) poke.o

.c.o:
	$(CC) -c $(ALL_CFLAGS) poke.c

install: poke
	cp poke $(bindir)/poke

clean:
	rm poke poke.o
Some problems with this approach should be clear.

Users who want to compile poke will have to know how `utime' works on their systems, so that they can uncomment the `Makefile' correctly.

The installation is done using `cp', but many systems have an `install' program which may be used, and which supports optional features such as stripping debugging information out of the installed binary.

The use of `Makefile' variables like `CC', `CFLAGS' and `LDFLAGS' follows the requirements of the GNU standards. This is convenient for all packages, since it reduces surprises for users. However, it is easy to get the details wrong, and wind up with a slightly nonstandard distribution.

Third Try
For our third try at this program, we will write a `configure.in' script to discover the configuration features on the host system, rather than requiring the user to edit the `Makefile'. We will also write a `Makefile.am' rather than a `Makefile'.

The only change to `poke.c' is to add a line at the start of the file:

#include "config.h"
The new `configure.in' file is as follows.

AC_INIT(poke.c)
AM_INIT_AUTOMAKE(poke, 1.0)
AM_CONFIG_HEADER(config.h:config.in)
AC_PROG_CC
AC_HEADER_STDC
AC_CHECK_HEADERS(utime.h)
AC_EGREP_HEADER(utimbuf, utime.h, AC_DEFINE(HAVE_STRUCT_UTIMBUF))
AC_FUNC_UTIME_NULL
AC_OUTPUT(Makefile)
The first four macros in this file, and the last one, were described above; see section Write configure.in. If we omit these macros, then when we run `automake' we will get a reminder that we need them.

The other macros are standard autoconf macros.

`AC_HEADER_STDC'
Check for standard C headers.
`AC_CHECK_HEADERS'
Check whether a particular header file exists.
`AC_EGREP_HEADER'
Check for a particular string in a particular header file, in this case checking for `utimbuf' in `utime.h'.
`AC_FUNC_UTIME_NULL'
Check whether `utime' accepts a NULL second argument to set the file change time to the current time.
See the autoconf manual for a more complete description.

The new `Makefile.am' file is as follows. Note how simple this is compared to our earlier `Makefile'.

bin_PROGRAMS = poke

poke_SOURCES = poke.c
This means that we should build a single program name `poke'. It should be installed in the binary directory, which we called `bindir' earlier. The program `poke' is built from the source file `poke.c'.

We must also write a `acconfig.h' file. Besides `PACKAGE' and `VERSION', which must be mentioned for all packages which use automake, we must include `HAVE_STRUCT_UTIMBUF', since we mentioned it in an `AC_DEFINE'.

/* Name of package.  */
#undef PACKAGE

/* Version of package.  */
#undef VERSION

/* Whether utime.h defines struct utimbuf.  */
#undef HAVE_STRUCT_UTIMBUF
Generate Files
We must now generate the other files, using the following commands.

aclocal
autoconf
autoheader
automake
When we run `autoheader', it will remind us of any macros we forgot to add to `acconfig.h'.

When we run `automake', it will want to add some files to our distribution. It will add them automatically if we use the `--add-missing' option.

By default, `automake' will run in GNU mode, which means that it will want us to create certain additional files; as of this writing, it will want `NEWS', `README', `AUTHORS', and `ChangeLog', all of which are files which should appear in a standard GNU distribution. We can either add those files, or run `automake' with the `--foreign' option.

Running these tools will generate the following files, all of which are described in the next chapter.

`aclocal.m4'
`configure'
`config.in'
`Makefile.in'
`stamp-h.in'
