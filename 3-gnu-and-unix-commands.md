# GNU && UNIX Commands
---
## Work on the Command Line
#### Bash Shell Environment
- Shells
  - bash generally default
  - csh
  - ksh
  - zsh
- Environment Variables
  - `CWD=/home/user/Documents`
- Bash Functions
- `env` -- View currently set environment variables
- `set` -- Display shell settings or shell variables for the session
- `unset` -- Remove a variable or custom bash function
- `shopt` -- Displays shell optinos and their current settings
- `export` -- Used to export a var to current shell and any new shells started from current shell
- `which` -- Locate an application file that is located within PATH
- `type` -- Used to determine if something is a func, file, alias, built-in, or keyword
- Weak quotes (" ") vs. Strong quotes (' ')
  - weak will echo variable info 
  - strong will echo literal 
  
#### Bash History and the Manual Pages
- `history -- shows most recentl yran commands
  - `!117` -- Runs numerical entry command from history
- `.bash_history` -- Contains the prev run commands
- `HISTFILESIZE` -- Env var that determines how many lines the bash history will contain
- Manual Pages Sections
  - 1 -- Executable programs or shell commands
  - 2 -- System calls - functions provided by the kernel
  - 3 -- Library calls - funcs within program libs
  - 4 -- Special files - typically those found in /dev
  - 5 -- File formats and conventions
  - 6 -- Games
  - 7 -- Misc items and conventions
  - 8 -- System admin commands, usually only for root
  - 9 -- Non standard Kernel routines
- `man -k` -- Search man pages for a specific keyword
- apropos -- Same as man -k
- man[section number] -- open particular section number for a specified command

---
## Process Text Streams using Filters
- `cat`
  - view, but also join text files
- `less`
- `head`
- `tail`
  - `tail -f` -- follows file/logs
- `zcat` -- view gzip compressed text files
- `bzcat` -- view bunzip2
- `xzcat` -- used to view xz compressed files
- Text File Statistics
  - `nl` -- number of lines
    - skips blank lines by default. -b includes, followed by a
  - `wc` -- word count
  - `od` -- octal dump; prints out a file in octal or other formats
- Message Digests
  - `md5sum` -- weakest of these algos
  - `sha256sum`
  - `sha512sum`
  
#### Text Manipulation
- `sort` -- used to sort and/or merge lines of a file
  - `-n` treates numbers as a whole not just the first letter
  - Doesn't actually change file, just displays
- `uniq` -- Displays unique lines of a file
- `tr` -- translate (or swap) characters in a file or another character
  - `-d` to delete a char
  - `'A-Z' 'a-z` changes case
- `cut` -- Etracts columsn or fields of data from a file
  - `-d` delimiter. default is tab, can specify a diff
- `paste` -- merges lines of files
  - also uses -d as a delimiter; tab is default. 
- **`sed`**
  - Stream editor
  - most basic op is search and replace
  - `sed 's/desktop/workstation/' list.csv`
    - replace desktop with workstation
    - /g at the end denotes globally
    - `-i` makes the change permanent
- `split`
  - Used to split a file up into individual pieces
  - By default each piece of the file contains up to 1k lines, but this can be changed to a different value or file size
  - splits by size `-b` into `xaa`, `xab`, etc.
  - `-d` changes numeric suffixes
  - `-n` number of files

---
## Perform Basic File Management


---
## Use Streams, Pipes, and Redirects


---
## Create, Monitor, and Kill Processes


---
## Modify Process Execution Priorities


---
## Search Text Files Using Regular Expressions


---
## Perform Basic File Editing Operations in VI
