Contents
===========
This directory contains tools for developers working on this repository.

check-doc.py
============

Check if all command line args are documented. The return value indicates the
number of undocumented args.

clang-format-diff.py
===================

A script to format unified git diffs according to [.clang-format](../../src/.clang-format).

Requires `clang-format`, installed e.g. via `brew install clang-format` on macOS.

For instance, to format the last commit with 0 lines of context,
the script should be called from the git root folder as follows.

```
git diff -U0 HEAD~1.. | ./contrib/devtools/clang-format-diff.py -p1 -i -v
```

copyright\_header.py
====================

Provides utilities for managing copyright headers of `The DogeCash
developers` in repository source files. It has three subcommands:

```
$ ./copyright_header.py report <base_directory> [verbose]
$ ./copyright_header.py update <base_directory>
$ ./copyright_header.py insert <file>
```
Running these subcommands without arguments displays a usage string.

copyright\_header.py report \<base\_directory\> [verbose]
---------------------------------------------------------

Produces a report of all copyright header notices found inside the source files
of a repository. Useful to quickly visualize the state of the headers.
Specifying `verbose` will list the full filenames of files of each category.

copyright\_header.py update \<base\_directory\> [verbose]
---------------------------------------------------------
Updates all the copyright headers of `The DogeCash developers` which were
changed in a year more recent than is listed. For example:
```
// Copyright (c) <firstYear>-<lastYear> The DogeCash developers
```
will be updated to:
```
// Copyright (c) <firstYear>-<lastModifiedYear> The DogeCash developers
```
where `<lastModifiedYear>` is obtained from the `git log` history.

This subcommand also handles copyright headers that have only a single year. In
those cases:
```
// Copyright (c) <year> The DogeCash developers
```
will be updated to:
```
// Copyright (c) <year>-<lastModifiedYear> The DogeCash developers
```
where the update is appropriate.

copyright\_header.py insert \<file\>
------------------------------------
Inserts a copyright header for `The DogeCash developers` at the top of the
file in either Python or C++ style as determined by the file extension. If the
file is a Python file and it has  `#!` starting the first line, the header is
inserted in the line below it.

The copyright dates will be set to be `<year_introduced>-<current_year>` where
`<year_introduced>` is according to the `git log` history. If
`<year_introduced>` is equal to `<current_year>`, it will be set as a single
year rather than two hyphenated years.

If the file already has a copyright for `The DogeCash developers`, the
script will exit.

gen-manpages.sh
===============

A small script to automatically create manpages in ../../doc/man by running the release binaries with the -help option.
This requires help2man which can be found at: https://www.gnu.org/software/help2man/

With in-tree builds this tool can be run from any directory within the
repostitory. To use this tool with out-of-tree builds set `BUILDDIR`. For
example:

```bash
BUILDDIR=$PWD/build contrib/devtools/gen-manpages.sh
```

github-merge.py
===============

A small script to automate merging pull-requests securely and sign them with GPG.

For example:

  ./github-merge.py 3077

(in any git repository) will help you merge pull request #3077 for the
dogecash/dogecash repository.
DogeCash-Project/DogeCash repository.

What it does:
* Fetch master and the pull request.
* Locally construct a merge commit.
* Show the diff that merge results in.
* Ask you to verify the resulting source tree (so you can do a make
check or whatever).
* Ask you whether to GPG sign the merge commit.
* Ask you whether to push the result upstream.

This means that there are no potential race conditions (where a
pullreq gets updated while you're reviewing it, but before you click
merge), and when using GPG signatures, that even a compromised GitHub
couldn't mess with the sources.

Setup
---------
Configuring the github-merge tool for the dogecash repository is done in the following way:
Configuring the github-merge tool for the DogeCash repository is done in the following way:

    git config githubmerge.repository dogecash/dogecash
    git config githubmerge.repository DogeCash-Project/DogeCash
    git config githubmerge.testcmd "make -j4 check" (adapt to whatever you want to use for testing)
    git config --global user.signingkey mykeyid (if you want to GPG sign)

optimize-pngs.py
================

A script to optimize png files in the DogeCash
repository (requires pngcrush).

fix-copyright-headers.py
===========================

Every year newly updated files need to have its copyright headers updated to reflect the current year.
If you run this script from src/ it will automatically update the year on the copyright header for all
.cpp and .h files if these have a git commit from the current year.

For example a file changed in 2014 (with 2014 being the current year):
```// Copyright (c) 2009-2013 The Bitcoin developers```

would be changed to:
```// Copyright (c) 2009-2014 The Bitcoin developers```

logprint-scanner.py
===================
LogPrint and LogPrintf are known to throw exceptions when the number of arguments supplied to the
LogPrint(f) function is not the same as the number of format specifiers.

Ideally, the presentation of this mismatch would be at compile-time, but instead it is at run-time.
A script to optimize png files in the DogeCash
repository (requires pngcrush).

This script scans the src/ directory recursively and looks in each .cpp/.h file and identifies all
errorneous LogPrint(f) calls where the number of arguments do not match.

The filename and line number of the errorneous occurence is given.

The script returns with the number of erroneous occurences as an error code to help facilitate
integration with a continuous integration system.

The script can be ran from any working directory inside the git repository.

symbol-check.py
===============

A script to check that the (Linux) executables produced by gitian only contain
allowed gcc, glibc and libstdc++ version symbols. This makes sure they are
still compatible with the minimum supported Linux distribution versions.

Example usage after a gitian build:

    find ../gitian-builder/build -type f -executable | xargs python contrib/devtools/symbol-check.py

If only supported symbols are used the return value will be 0 and the output will be empty.

If there are 'unsupported' symbols, the return value will be 1 a list like this will be printed:

    .../64/test_dogecash: symbol memcpy from unsupported version GLIBC_2.14
    .../64/test_dogecash: symbol __fdelt_chk from unsupported version GLIBC_2.15
    .../64/test_dogecash: symbol std::out_of_range::~out_of_range() from unsupported version GLIBCXX_3.4.15
    .../64/test_dogecash: symbol _ZNSt8__detail15_List_nod from unsupported version GLIBCXX_3.4.15

update-translations.py
======================

Run this script from the root of the repository to update all translations from transifex.
It will do the following automatically:

- fetch all translations
- post-process them into valid and committable format
- add missing translations to the build system (TODO)

See doc/translation-process.md for more information.
