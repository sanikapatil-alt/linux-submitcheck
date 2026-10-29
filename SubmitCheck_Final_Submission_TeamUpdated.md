# SubmitCheck

> `submitcheck` --- checks a student assignment folder for required
> files and flags empty files before submission.

**Team:** Sanika Patil and Sneha Sujeesh\
**Repository:** Not created yet\
**Date:** 29-10-2026

------------------------------------------------------------------------

## 1. The problem

### What we keep doing by hand

Before submitting coursework, a student may manually inspect an
assignment folder, check that required files exist, and open files to
make sure they are not empty. This can be repetitive when an assignment
contains multiple files.

### Evidence that this is real

**Evidence collected from the current machine:** Terminal tests on the
sample folder showed that the first run detected `report.pdf` as missing
and flagged `data.csv`, `main.py`, and `README.md` as empty. After
adding content and creating a non-empty `report.pdf` placeholder, the
command reported `READY TO SUBMIT`, with zero missing and zero empty
files.

Save your terminal screenshots with the project before submission. The
sample PDF is only a text placeholder, not a valid PDF.

### How often does this happen, and what does it cost?

The prototype demonstrates the checks using a prepared sample folder.
The real frequency and time cost have not been measured yet. To make
this claim evidence-based, time one manual check and one command run and
record the actual results.

### Who else has this problem?

This may affect students submitting multiple files, but classmate
feedback has not yet been collected. Ask one or two classmates whether
they have missed or submitted empty files, then record their actual
responses.

------------------------------------------------------------------------

## 2. Prior art --- what already exists

### Tools to compare

These tools overlap with parts of SubmitCheck. The descriptions below
are general knowledge; **the tools have not all been tried hands-on for
this project yet**.

  -----------------------------------------------------------------------
  Tool              Where we found it What it does      Why it does not
                                                        fully solve this
                                                        use case
  ----------------- ----------------- ----------------- -----------------
  Bash `test`       `help test`       Checks whether a  Requires separate
                                      path exists, is a checks and user
                                      file, or is       interpretation
                                      non-empty         

  `find`            `man find`        Searches files    Does not itself
                                      and directories   provide the
                                      using criteria    project's
                                                        predefined
                                                        checklist and
                                                        summary

  `file`            `man file`        Identifies file   Does not provide
                                      types from their  an assignment
                                      contents          checklist report
  -----------------------------------------------------------------------

### Evidence of trying them

Run these commands and capture their real output before claiming
hands-on testing:

``` bash
help test
man find
man file
test -f ~/SubmitCheck/demo_assignment/main.py && echo "main.py exists"
find ~/SubmitCheck/demo_assignment -maxdepth 1 -type f -print
file ~/SubmitCheck/demo_assignment/*
```

The shell tools provide useful building blocks, but they require
separate commands. SubmitCheck combines a small set of checks into one
readable report.

### Where we searched

-   [x] Local macOS terminal tools used to build and test the prototype.
-   `man -k` search for relevant utilities --- not yet completed.
-   GitHub search --- not yet completed.
-   Homebrew search --- not yet completed.
-   Stack Overflow search --- not yet completed.
-   Asked a classmate or instructor --- not yet completed.

### The gap, in one sentence

Existing shell tools can test individual files and search directories,
but SubmitCheck combines required-file and empty-file checks into one
readable report tailored to a student assignment folder.

------------------------------------------------------------------------

## 3. What we are building

### In scope

-   Check for required files: `main.py`, `README.md`, and `report.pdf`.
-   Report missing required files with `[FAIL]`.
-   Flag zero-byte files in the target folder with `[WARN]`.
-   Print missing-file and empty-file counts and an overall result.
-   Accept a folder path as an argument.

### Out of scope

-   It does not judge whether file contents are correct or high quality.
-   It does not verify that `report.pdf` is a valid PDF; it checks
    existence and non-zero size only.
-   It does not scan subfolders recursively.
-   It does not upload or submit files automatically.

### How we will know it worked

The tool succeeds if one command reports missing and empty files
correctly in both incomplete and complete test cases, and can be reused
on another folder.

------------------------------------------------------------------------

## 4. Tech stack

  -----------------------------------------------------------------------
  Choice                  What we picked          Why, and what we
                                                  rejected
  ----------------------- ----------------------- -----------------------
  Language                Bash                    Available in macOS
                                                  Terminal and suited to
                                                  filesystem checks;
                                                  Python would add
                                                  another runtime for
                                                  this small task

  Argument parsing        First positional        Simple for a small
                          argument; defaults to   prototype; a full
                          `.`                     option parser is not
                                                  needed yet

  Other dependencies      Standard shell file     No third-party packages
                          tests and Bash features required

  Testing                 Prepared demo folder    Reproducible test
                          with missing, empty,    cases; a formal
                          and non-empty files     automated test suite is
                                                  future work

  Packaging / install     Copy to `~/bin` and add Simple per-user setup
                          it to `PATH` in         on this Mac
                          `~/.zshrc`              
  -----------------------------------------------------------------------

### Why this language

Bash is suitable because the task is about checking files and folders
from the command line. It is already available in the user's macOS
environment and provides file tests, loops, conditions, and terminal
output without third-party libraries. The current script is compatible
with the Mac's Bash 3.2.

### What we are deliberately not using

We are not using Python or a database because the tool only inspects
local files and prints a report. Bash keeps the project focused on
command-line automation.

------------------------------------------------------------------------

## 5. Architecture

``` mermaid
flowchart TD
    A[User runs submitcheck folder_path] --> B[Choose target folder]
    B --> C{Folder exists?}
    C -->|No| D[Print error and exit]
    C -->|Yes| E[Check required filenames]
    E --> F[Report missing files]
    F --> G[Check files in target folder]
    G --> H[Flag zero-byte files]
    H --> I[Print counts and final status]
```

### Components

  -----------------------------------------------------------------------
  Part                    Responsibility          File
  ----------------------- ----------------------- -----------------------
  Command script          Validate folder, check  `submitcheck.sh`,
                          required files and      installed as
                          empty files, print      `~/bin/submitcheck`
                          report                  

  Demo folder             Sample files for        `demo_assignment/`
                          repeatable tests        

  Presentation            Explains problem,       PowerPoint file
                          design, demo,           
                          advantages, and         
                          limitations             
  -----------------------------------------------------------------------

### Data flow

The user supplies a folder path, or the script uses the current
directory by default. The script checks the three required filenames,
then examines files directly inside the target folder and flags
zero-byte files. It prints a report and does not modify files.

### What happens when things go wrong

  ---------------------------------------------------------------------
  If this happens                    The tool does this
  ---------------------------------- ----------------------------------
  Target folder does not exist       Prints an error and exits with
                                     status `1`

  Required file is missing           Prints `[FAIL]` and increments the
                                     missing count

  File is empty                      Prints `[WARN]` and increments the
                                     empty count

  No missing or empty files are      Prints `READY TO SUBMIT`
  detected                           
  ---------------------------------------------------------------------

------------------------------------------------------------------------

## 6. Usage

### Synopsis

``` bash
submitcheck [folder_path]
```

### Commands and flags

  Argument        What it does               Default
  --------------- -------------------------- -------------------------
  `folder_path`   Folder to check            Current directory (`.`)
  No argument     Checks current directory   `.`

The current prototype does not implement `--help`, `--dry-run`, or
configurable required filenames. It accepts a positional folder path
only.

### Examples

**Basic use**

``` bash
submitcheck ~/SubmitCheck/demo_assignment
```

**Observed incomplete-folder output pattern**

``` text
[PASS] main.py exists
[PASS] README.md exists
[FAIL] report.pdf is missing

[WARN] data.csv is empty
[WARN] main.py is empty
[WARN] README.md is empty

Result: SUBMISSION NEEDS ATTENTION
Missing files: 1
Empty files: 3
```

**Observed complete test-folder output pattern**

``` text
[PASS] main.py exists
[PASS] README.md exists
[PASS] report.pdf exists

Result: READY TO SUBMIT
Missing files: 0
Empty files: 0
```

The second test used non-empty placeholder text in a file named
`report.pdf`. It is not a valid PDF; PDF validation is outside the
current prototype.

**When the folder path is invalid**

``` bash
submitcheck ~/SubmitCheck/folder-that-does-not-exist
```

Expected behavior: print `Folder does not exist.` and exit with status
`1`.

### Exit codes

  ---------------------------------------------------------------------
  Code                               Meaning
  ---------------------------------- ----------------------------------
  `0`                                Script completed and printed a
                                     report; current prototype also
                                     uses this for reports needing
                                     attention

  `1`                                Target folder does not exist

  Other codes                        Not deliberately defined
  ---------------------------------------------------------------------

**Future improvement:** return a non-zero status when required files are
missing or empty so other scripts can detect a failed check.

### Installation

The prototype was installed for the current user on macOS:

``` bash
mkdir -p ~/bin
cp ~/SubmitCheck/submitcheck.sh ~/bin/submitcheck
chmod +x ~/bin/submitcheck
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Test it:

``` bash
submitcheck ~/SubmitCheck/demo_assignment
```

A fresh-machine installation test and repository URL are still pending.

------------------------------------------------------------------------

## 7. References

-   [GNU Bash Reference
    Manual](https://www.gnu.org/software/bash/manual/) --- Bash syntax
    and shell behavior.
-   `help test` --- shell file tests.
-   `man find` --- filesystem search options.
-   `man file` --- file-type identification.

**Evidence note:** The prototype has been built and run on macOS.
Prior-art hands-on comparison, peer feedback, and independent
installation testing remain to be completed. Do not mark these
activities complete until they have actually been done.

## Final project status

SubmitCheck is a working Bash prototype demonstrated on macOS with an
incomplete test folder and a corrected test folder. The report is
transparent about its limitations: the sample PDF is not validated,
required filenames are fixed, and broader prior-art research and peer
feedback have not been completed. The project demonstrates practical
command-line automation by combining existing shell features into a
single reusable assignment-checking command.
