# Command Line Class! Day of Shecurity, 23 March 2021

## Linux Command Line Emulator
[Specific emulator](https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192)

[Show me all the emulators!](https://bellard.org/jslinux/)

## Why learn command line stuff?
* Some things are just faster to do this way than via a graphical user interface (GUI; think a browser, or moving folders and windows around)
* If you work with Linux servers, you will mostly be dealing with the command line
* You look like a super-cool hacker
* Being better at this stuff helps with literally every engineering job, security or otherwise
* A CTO at my previous job: "You will never be sorry to know more Bash." True!

## Different flavors of command lines
* Linux and Mac share lots of commands, but they can vary in execution (flags, available options)
* And there are lots of different flavors of Linuxes too, of course
* I've worked with Red Hat Enterprise Linux (RHEL) and Debian the most, but Ubuntu is also quite common. Kali Linux is Linux, but it's a pretty different system for a different purpose (pen testing, with lots of rad tools)
* If in doubt about what a command offers in a certain system, `man $command` to see the man[ual] page for it. 

## Outside concerns (Sir Not-Appearing-in-This-Presentation)
It's a big subject. I had to stop somewhere. We won't be touching on: 
* PowerShell and anything Windows
* zsh, fsh, and all the other shells

## Some jargon
Server == box == machine… but I will try to only say server, I promise.

`$thing` is Bash notation for a variable. I use it to mean "add your own needed term here."

Bash is usually the term used for the language. The command line or shell is what we're typing into. These terms sometimes get used in overlapping ways, though.

In this repo, if something is a command, it looks like `this`.
That means it’s something you can paste into the command line and have it mean something. If it’s a key (spacebar, escape, ctrl), it’s in regular font.

## Other useful resources
* Man[ual] pages (including man bash)
* Search within a man page with `/$searchTerm`
* [Explainshell](explainshell.com)
* Google "best $command commands" - people LOVE writing useful posts about this stuff (and put things in explainshell before you run them on your own machine, please)
* symbolhound.com if googling things like "bash >" is tricky

## A few command line niceties
* Control-C (or: omg the scrolling is going to set my computer on fire)
* When and why to have multiple terminal tabs connected to the same server
* `sudo !!` (and `sudo` generally)
* Tab completion
* `cd --`
* Beware of curly/smart quotes. Nice in typography, possible nightmares on the command line
* `.` can usually be used to mean “this directory.” Think of it as shorthand for "here."

## Let's build a command!
`ls` == "list all the files in a given directory"

`ls -al` == "list all the files in a given directory, including hidden ones, with long output" (-al are flags)

`ls -al ./dir/subdir`
`ls -al /lib`
`ls /lib /dir /cat_pix`

These are arguments. The first one uses a relative path (file is at this location, starting at my working directory), the second uses an absolute path (starting with /, or root), and the third has multiple absolute paths. 
`ls -al . | grep DoS` == "list all of the files in my current working directory with long output, then show me all files that include 'DoS'"

`ls --help` == "Show me all the flags, syntax, and any other goodies you've got."n

## What do you know about `ls` now?
* `ls` means list files
* `ls` can take both absolute and relative paths as arguments (including both at once!)
*  `ls` has lots of flags, and you can layer them all on after a dash 
* `ls --help` will show you so many options

## How do I learn about a server? Or: fun with OSINT
* `nmap -F 8.8.8.8` (or any IP; try on your own computer)
* `curl -i` $url
* `whois` and `dig`

`nmap` tells you what ports are open, which can reveal vulnerabilities and what’s running on a server. `-F` scans just the most common ports (what we're doing today for brevity's sake).

`curl` can be used to download whole webpages, but with the `-I`, it only gives you headers, which can tell you server software versions and other details.

`whois` can give you host details from a URL.

`dig` can give you DNS details, including DNS records and the IPs of all servers associated with that URL.

## How do I connect to a server?
`ssh -i ~/.ssh/your_key.pem`

### Let's break that down
`ssh`: the command

`-i ~/.ssh/breanne_dos.pem`: `-i` says I’m using an identity (key) file, the path is where the file is

`ec2-user`: the Linux user I’m logging in as. This is standard AWS stuff.

`@34.218.26.14`: the public IP of my server

## How do I navigate this server?
* `ls` again!
* `cd --` too
* `history` or ctrl-r for reverse-i-search
* `whoami` and `pwd`
* `cat $file` to print a file’s contents to the command line

## How do I see what’s going on in the server?
* `top`: a live-updated display of a server's running processes (Type `q` to exit - this works for lots of command line programs that take over the whole window)
* `ps aux`: also good, but doesn’t update live (but can be grepped!)
* `lscpu`: tells you details about what the server's architecture

## How do I affect what’s going on in the server?
Altering processes, including kill
* `killall $processName` if you have the process name
* `kill $pid` if you have the PID
* `kill -15 $pid` for a graceful termination
* `kill -9 $pid` for PROCESS DEAD THIS INSTANT PLEASE

Running your own code
* `python3 your_script.py`

## How do I navigate this big log file?
`less $filename [$otherFilename $anotherFilename]`
* Scroll or arrow to go up or down
* Spacebar or `f` to go forward one page
* `b` to go backward one page 
* `/$pattern` to search forward, `?$pattern` to search backward
* `q` to quit (there it is again!)
* SO MANY MORE

`tail -f $filename` to have it show additions to the file in real time (great for active logs)

## How do I find a needle in this ~~haystack~~ big log file?
`grep`! 

Three lines of context before and after: `grep -B 3 -A 3 "$word" $file`

Combine `history` with `grep`: `history | grep $command`

`grep` challenge!

## Pipes!
* Piping to `grep`: `cat $file | grep $searchTerm`
* Piping to `grep` and then `wc` (word count, but does more than that): `cat $file | grep $searchTerm | wc -l`
* Piping to `head` and `tail`: `cat $file | head -5 | tail -5`

## > and >>
Bash redirection!

`>>` is append (append == a friend)
* `cat logfile.txt > otherlogfile.txt`

This puts the text of logfile.txt at the end of otherlogfile.txt, preserving the second file’s original contents.

`>` is overwrite (DANGER, DANGER)
* `cat logfile.txt >> otherlogfile.txt`

This obliterates the previous contents of otherlogfile.txt, overwriting them with logfile.txt. If otherlogfile.txt doesn’t exist yet, it creates it. 
IF IN DOUBT: >>

## How do I cover my tracks or otherwise change things?
### Logs you might want to alter: 
* /var/log/auth.log
* /var/log/secure
* /var/log/messages

### How to delete history
* `history -d $lineNumber` to take out just one line
* `history -c` to nuke the whole thing from orbit (not a subtle move, but it can work)

### Edit files with vi
* `vi $filename` to edit (`touch $filename` to create - like the ceiling of the Sistine Chapel)
* Type `i` to type (think insert)
* Type escape + `:x` to save and exit (NOW YOU KNOW THE SECRET)
* Type escape + `:q` to exit without saving

That’s enough on that for today, I promise.

## Some resources, from an internet that's full of them
* [Vim Adventures](https://vim-adventures.com/)
* [man bash](https://linux.die.net/man/1/bash)
* [My first Bash tutorial](https://ryanstutorials.net/bash-scripting-tutorial/)
* [Codecademy for Bash](https://www.codecademy.com/catalog/language/bash)
* [Exercism for Bash](https://exercism.io/tracks/bash)
* [Bash cheatsheet](https://devhints.io/bash)
* [Pacific Hackers on OSINT](https://www.youtube.com/watch?v=aut8p19xeK8)
* [More on less](https://linuxize.com/post/less-command-in-linux/)
* [Julia Evans on Linux](https://jvns.ca/#linux-debugging---tracing-tools) and a [command line zine](https://jvns.ca/blog/2018/08/05/new-zine--bite-size-command-line/)
* Me on [$PATH and system variables](https://truss.works/blog/2016/2/26/engineer-how-to-access-and-edit-your-path-system-variable)