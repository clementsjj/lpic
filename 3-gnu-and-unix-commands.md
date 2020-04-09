# GNU && UNIX Commands
---
## Index
- Bash Shell Environment
- Bash History and the Manual Pages
- Text Manipulation
- Working With Files
- Working With Directories
- File and Folder Compression
- Finding Files
- File Globbing
- Use Streams, Pipes, and Redirects
- Create, Monitor, and Kill Processes
- Modify Process Execution Priorities
- Search Text Files Using Regular Expressions

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
#### Working With Files
- ls, touch, cp, rm, mv, file
- touch used to modify a file's time stamp, but most commonly to create an empty file
- `file` -- try to determine a file's type

#### Working With Directories
- `cd -` will go to last working directory, as indicated by $OLDPWD 
- `mkdir -p` required to specify parent dir when specifying a directory you are not already in

#### File and Folder Compression
- `dd`
  - Copies and converts files Often used to create files of arbitrary size and back up disk drives
  - `dd if=boot.img of=/dev/sdc`
    - if = input file
    - of = output file
  - `dd if=/dev/xvda of=/tmp/mbr.img bs=512 count=1`
    - backup master boot record
  - `dd if=/dev/urandom of=file bs=1024k count=10`
- `tar`
  - wraps up files and folders into an archive file. Does not provide compression on its own!
  - "tape archive". often called tarballs
  - `-tf` -- will show you the files in a tarball
  - `-xf` -- will extract files
- `gzip`
  - creates `.gz` compressed files
  - `tar -czf` will zip, or `gzip file`
  - generally named `.tar.gz` or `.tgz`
- `gunzip`
  - extracts `.gz` comporessed files
  - `tar -xzf`
- `bzip2`
  - creates `.bz2` compressed files
  - `tar -cjf` or `bzip2 file`
- `bunzip2`
  - extracts `.bz2` compressed files
  - `tar -xvjf` (v is verbose)
- `xz`
  - creates `.xz` compressed files
  - Popular in red hat
- `unxz`
  - extracts `.xz` compressed files

#### Finding Files
`find`
- generally expensive on the cpu since it is searching the file system at that exact time
- `-name` -- find files based on their name
- `-ctime` -- find files based on the time when they were last changed, 24 increments
- `-atime` -- find files based on their access time
- `-newer`
- `-type`
- `-empty` -- find files or folders that are empty
- `-exec [command] {} \;`
  - \; closing out the exec command
  - {} placeholder for items to be execd

#### File Globbing
- originates from "Global command"
- `*` -- wildcard for zero or more chars
- `?` -- stand in for one char at a time
- `[abc]` -- any chars in list
- `[^abc]` -- any one char except thos in the list
- `[0-9]` -- matches a range of nums


---
## Use Streams, Pipes, and Redirects
- Standard Input, Output, and Error
  - `stdout` 
    - bucket of output redirected to screen, or can skip bucket and go to file
    - file handle 1
  - `stdin`
    - can chain inputs for another input
    - `wc test.sh` input comes from keyboard
    - `wc < test.sh` -- input comes from file
    - file handle 0
  - `stderr` 
    - file handle number associated with it is 2
    - `ish.sh 2> error.log`
      - stderr gets redirected to error.log file
    - `ish.sh 2>&1 | less` 
      - stderr and stdout gets sent as stdin to the less command
      
- Redirecting Output to the Screen and a File
  - `tee`
    - Reads data from stdin and writes data to stdout and files.
    - Useful for chaining together long commands and viewing output at various stages
    - `ls /tmp/share/lib[Xx]* | tee lib-docs.txt | sort -r | tee lib-docs-reverse.txt`
      - will not however have the output of the first tee 
  - `xargs`
    - accepts input from stdin and other commands
    - commonly used with the find command (but can be used with others)
    - takes stdout from one command and use it as stdin on another command as options
    - `find test/ -empty | xargs rm -f
      - why not -exec? it will handle files one at a time, can take a long time
      - xargs creates a sublist instead of acting on each file one at a time. gives it the ability to act on all of the files in one swoop. much faster.
    - `grep -l "junk" test/file_* | xargs -I {} mv {} test/bak/`
    - `find ~ -name "*.sh" | xargs ls -al > scripts.txt`
  
---
## Create, Monitor, and Kill Processes
- Reviewing the state of Your System
  - A process is a set of instructions loaded into memory
    - memory context, environment, a priority that dictates how much cpu time
    - Kernel hand over control to PID 1 (process id)
    - primary process used to be init, now systemd
    - PID1 is the parent process. all others after are child processes
    - `ps` -- View the process hierarchy
    - pid2 = kernal space from kernel itself... 
    - pid1 is known as user space. where we interact as users
    - just running ps itself just shows bash and ps
    - gets info from /proc directory
   
- Monitoring Processes
  - `uptime`
  - `free`
  - `pgrep`
    - acts like ps, but find process info based on process name
    - returns process ids by default, use -a for more
    - `pgrep -a httpd`
  - `kill`
    - send signal (usually sigterm) to a process based on PID
  - `pkill`
    - Send a signal to a process based on process name
  - `killall`
    - kills all processes based on a name provided as an argument

- Keep a Process Running
  - `watch`
    - runs a command at specified intervals. Used to monitor a command's output
    - `watch -n 1 date`
  - `screen`
    - terminal window manager that allows you to run commands in an isolated session
  - `tmux`
    - modern terminal window manager like screen with extra features 
  - `nohup`
    - Command that is preceded by nohup receives signal 1 so that should a terminal window close, the process will still run as long as the login session is not terminated
    - `nohup ping www.google.com &`
  - `bg`
    - sends a job to the background while it keeps running
  - `&`
    - when appled to the end of a command, the command is sent to the background returning the use of the shell to the user
  - `fg` 
    - brings a job that is in the background into the foreground
  - `jobs`
    - displays a listing of jobs that are in the background
    
---
## Modify Process Execution Priorities
- All process need to use cpu time
- Priority assignment dictate how much time the cpu grants the process
- Process priority is called **nice level**
  - -20 = highest priority
  - 19 = lowest priority
  - 0 = default for most processes
- Only root can lower the nice value of a process
- `nice`
  - define new nice level BEFORE an application is started
  - `ps -o pid,nice,cmd,user`
- `renice`
  - used to change the nice level of an application that is already running
  - `sudo renice -n -1 1390`

---
## Search Text Files Using Regular Expressions
- . -- represents a single char
- ^ -- beginning of line
- $ -- search the end of a line
- [abc] -- search for specified chars
- [^abc] -- serach for other chars
- * -- match zero or more of the preceding chars or expression
- `man 7 regex` -- manual pages for regular expressions
- Precede with `grep`

- Regular Expression Tools
  - `sed`
  - `egrep`
    - command that searches a specified file line by line, returning lines that contain a pattern matching a given regular expression.
    - Equivalent to `grep -E`
  - `fgrep`
    - searches based on strings rather than patterns. Also uses file globbing instead of regular expressions. 
    - Equivalent to `grep -F`
---
## Perform Basic File Editing Operations in VI

