## ENSE 370 - Software Systems Design - Laboratory

# Lab 1: Introduction to CLI and Snoopy

### University of Regina
### Faculty of Engineering and Applied Science - Software Systems Engineering

### Lab Instructor: [Adam Tilson](mailto:Adam.Tilson@uregina.ca)

---

## Introduction

This lab gives a refresher on Unix-like Command Line Interfaces, and teaches you how to copy and submit your files to Snoopy. This is a requirement to submit all your labs and assignments for this class.

Unix is an Operating System (OS) developed at AT&T in the early 1970's, to facilitate multi-user remote access to a central mainframe. It was later branched and extended as BSD, Linux and MacOS, with desktop and mobile variants. We call these Unix-like operating systems as they permit the same functionality as Unix. While these systems typically support Graphical User Interfaces (GUIs), we are going to focus on the command line.

The command line may feel slow and unintuitive at first, but with practice it can become quicker and more convenient than working in a GUI for programming tasks. 

---

## Where can I use it?

Most operating systems already have a terminal which is capable of performing Unix-like commands.

- Windows: Windows Subsystem for Linux 2
- Mac:     Terminal
- Linux:   Terminal

University resources:
- Hyperion: The University mainframe at uregina.ca
- Snoopy: a Software Engineering Student server

You can also get commercial resources:
- e.g. an Amazon Web Service (AWS) Elastic Cloud 2 (EC2) instance running Linux. This typically costs ~5.00 / month.

My approach: 
- Linux Mint on a Virtual Machine.

---

## Sessions

When you first arrive at the command line, you are attempting to start a new session. You may need to log in. You'll have a username and password.

Unix environments typically do not show *'s when you type in passwords. The password is still being typed.

Each instance of the terminal window is a separate session. One user can have multiple concurrent sessions on the same machine at the same time, and multiple users can have sessions on one machine at a time. Considering this as the core functionality of the operating system further explains some of the design decisions of the OS. One advantage of this is that it allows you to run different processes at the same time, e.g. compiling code, and editing with a text editor in two different shells at the same time. 

---

## Kernels vs Shells

Kernels and Shells are different parts of the OS. The Kernel is the part of the OS actually runs the process, allocates resources and handles scheduling. If you take an OS course, you'll learn a bunch about kernels.

We are going to focus more on shells. Shells are how users may issue commands to the kernel, such as navigating the file system and starting applications.

Some historic shells:
- the Thompson shell (sh)
    - the Bourne shell (sh)
    - the korn shell (ksh)
- the c-shell (csh)
    - the tee-shell (tcsh)


Modern shells:
- the Bourne-again shell (bash)
    - the Z shell (zsh)

These all have similar major functionality, with some minor differences. The biggest differences appear in scripting, which is beyond the scope of this lab. For example, in the c-shell variants, the script syntax is inspired by c code.

To figure out the default shell:

```
echo $SHELL
```

You may also find the current running process with:

```
echo $0
```
...which works based on the POSIX standard.


You may switch to another installed shell from inside your current shell, e.g. open bash by running `bash`, which actually runs a shell inside a shell... Shell-ception. When you `exit` out you'll go back to the previous shell.

---

## Secure Shell

SSH is a protocol for securely connecting to a remote computer. Functionally, this is similar to opening up a shell inside of a shell, but instead you are opening a
shell on a different computer.

Basic usage:

```
ssh username@server.com 
```

You will then be prompted for your password.

If this is your first time connecting, you may also need to confirm the identity of the server.

Some ssh servers require key-based authentication instead of a password. To use this, you will generate a public and private key files, storing the public key on the server and the private key on the client. When you authenticate you will provide the private key in lieu of a password. You'll learn more about authentication in other SSE courses.

---

## Tunneling

For security purposes, some networks are configured such that a firewall prevents you from connecting directly to a given computer. You must instead first connect to a common access point, and then connect to the desired computer, via two hops. The university enforces this policy for improved network security.

Thus you must connect to Hyperion before you can connect to snoopy, in two hops.

```
               ssh            ssh
local computer --> uregina.ca --> snoopy.engg.uregina.ca 
```

You log in to uregina.ca using your novel credentials. (The same as URCourses.)

```
ssh username@uregina.ca
```

You log in to snoopy using your snoopy credentials. (Provided by Dr. Yow)

```
ssh username@snoopy.engg.uregina.ca
```

when you are finished with your session, you may use the command
```
exit
```

which will bring you back out one level. As it took two hops to get to snoopy, you will need to `exit` twice.

ssh versions >= 7.3 let you do this in one step using the ProxyJump (-J) flag.

An alternative to tunneling is to connect to a Virtual Private Network (VPN) to access the remote server.

---

## Commands

The fundamental way of interacting with a kernel through a shell is by issuing commands. These are of the form:

```
command     options     option parameter    command parameters
ls          -w          20                  /home
```

command - these are the actual programs that we are running, which provide some functionality.

options - these specify how the commands will behave. Some commands have optional options, these are like optional named arguments for a function in a programming language. Some commands have multiple options.

Some options have long and short names. The short names can sometimes be combined.

Some options require specific parameters. These types of options can't be combined, as it would be ambiguous which parameters went with which options.

The final parameter is the parameter for the command.

The first time you try this it may sounds unintuitive, but it gets easier with practice.


For example, let's consider the `ls` command, which lists the contents of a directory:

e.g. just the command
```
ls
```

A command with a long-form option which does not require a parameter
```
ls --all
```

A command with a short-form option which does not require a parameter
```
ls -a
```

A command with multiple long and short form options which do not require parameters 

```
ls --all -l --human-readable
```

A command with a combination of short-form parameters combined into a single statement
```
ls -alh
```

A command with a long form option and an option parameter
```
ls --width 20
```

A command with a long form option and a option parameter
```
ls -w 20
```

A command with a command parameter

```
ls /home
```

A combination of parameters in different configurations.

```
ls -alh --width 20 /home
```

As another example of something I do a lot - compressing audio with FFMPEG. FFMPEG is an media encoding utility with lots of functionality, but it all depends on using the correct options and parameters. 

```
ffmpeg -i input.wav -c:a libmp3lame output.mp3
```

Breaking it down:

```
ffmpeg -i input.wav -c:a libmp3lame output.mp3
^      ^  ^         ^    ^          ^          
|      |  |         |    |          Parameter for ffmpeg, output file
|      |  |         |    Parameter for the second option, lame codec
|      |  |         Second option, audio codec
|      |  Parameter for first option, name of the input file
|      First option, input 
Command
```

Some commands have multiple command parameters, e.g. source and destination:

```
cp source.txt destination.txt
```

The easiest way to learn about a command is through the `man` pages, e.g. 
```
man ls
```
---
## Secure Copy

Secure Copy (scp) is an application for copying files to and from a remote server over the ssh (or sftp) protocol. It works like copy, but additionally requires parameters specifying the remote location.

Basic usage: 
```
scp source destination
```
where local files are described the same way they would be on a local disk, but remote files are described with the username@server.com syntax, a colon :, and then the file or directory location on the remote server.

We can also use tunneling with scp as well, to copy a file in two jumps.

Here are six common use cases which you can use as a basis for constructing your own commands:

1. Local file to remote file
```
scp file.txt username@server.com:/remote/directory
```

2. Remote file to local file
```
scp username@server.com:file.txt /local/directory/
```

3. Local directory to remote
```
scp -r /local/directory/ username@server.com:/remote/directory/
```

4. Remote directory to local

```
scp -r username@server.com:/remote/directory/ /local/directory/
```

5. Local directory to remote with tunneling

```
scp -r -o ProxyJump=username@proxy.com /local/directory/ username@server.com:/remote/directory
```
* Remember to use your proxy password first, and your remote password second!


6. Remote file to local with tunneling

```
scp -o ProxyJump=username@proxy.com username@server.com:file.txt /local/directory/ 
```

Fill in these two yourself


7. Local file to remote with tuneling


8. Remote directory to local with tuneling

You can also use a graphical application such as WinSCP, or on Linux connect to a remote directory through some graphical file browsers.

---

## In terminal editors

So you successfully copied your files to the server, but now you need to make a last minute change, and would rather not download it again, make changes, and re-upload. Can we edit it directly on the server?!

Terminal editors to the rescue! These are text editors which run right in the shell.

Here are some in-terminal editors, from historical to modern

- `ed` - The original. Primitive, but technically functional.
- `vi` - visual editor. Thanks, I hate it.
- `vim` - a improved drop in replacement for vi. Steep learning curve.
- `emacs` - Powerful editor with macros. Veteran of the editor wars.
- `pico` - pine composer. 
- `nano` - a better drop-in replacement for pico, so named because nano
is 1000 times bigger. Hooray more puns. 

Where to start? If you only plan to do this once in a while, just use nano. If you would like to invest some time into learning, learn emacs or vim.

The best thing about nano - we can see all the important commands at the bottom of the screen. Nice! With both emacs and vi you will need to learn some key combinations just to do common operations like save a file and quit the editor.

Nano is sufficient to get you out of the occasional sticky situation.

Simple operations

open nano for a new or existing file:

```
nano filename.txt
```

Edit the file:
```
just start typing!
```

Navigate the file:
```
Arrow keys, page up, page down
```

exit the file:
```
^x (ctrl+x)
```

save the file on exit   
```
Save modified buffer? Y
File name to write? filename.txt <enter>
```

---
## Navigating the file system

You will learn more about the Unix filesystem in other courses. What you need to know is that user files are typically stored in:

```
/home/username
```

...where username is your username.

You can find where you are with:
```
pwd
```

You can change to a new directory relative to where you are with:

```
cd subdirectory
```

You can also apply some shortcuts to the directory:

- `/` represents the root directory
- `.` represents the current directory
- `..` represents the parent directory
- `~` represents the user home directory
- `-` takes you back where you were before 

Note: `-` can only be used cd, whereas the others can be used any time you are doing file operations.

---

## Filename conventions

When naming files on a Unix-like system, we typically use one of two standards:

```
lower-case-seperated-by-dashes.txt
```

or

```
lower_case_seperated_by_underscores.txt
```

Which is slightly more readable, though slightly more painful to type. Pick one and remain consistent!

Spaces aren't allowed unless you escape them, e.g. "\ "

---

## Common Commands
Here are some commands for common tasks. You will learn more of these in other classes. You can get some more information about these using `man`.

`touch` - create files

`cat` (concatenate) - read files and display them to the screen

`more` - display a file to the screen, with navigation

`less` - an improved, drop in replacement for more. More programming jokes! On modern systems, more is typically an alias for less - if you type in more you get less.

`mkdir` - make a new directory. Use the -p flag to also make required parent directories.

`mv` - move a file. Can also be used for renaming if used on a file in the same directory. By default, move will overwrite, which can be dangerous

`cp` - copy. The -r (recursive) flag can be used to copy directories

`rm` - delete a file with no way to recover. The -r flag applies this to directories. Use with caution!

`find` - finds files based on a search criteria, e.g.

```
find /home -name "*.jpg"
```
`echo` - print something to the screen

---

## Conveniences

The `tab` key can be used to autocomplete a command, including searching for files in the local directory

The `up arrow` and `down arrow` can be used to move through your command history

You can also see your command history with `history`, and rerun a command with `!number`, e.g. `!30`.

---
## Potential take away's from this lab...
- Open source software grows iteratively
- Eventually, good competing standards will spark debate
- Programmers have a sense of humour (debatable)
- Unix-like command lines aren't going away, and can actually make your life better as a programmer, at least for some things

---

## Assignment

Phase 1: Must be completed before the end of the lab:
- Log into Snoopy
- Create a HelloWorld file in Java using an in-terminal editor (nano, vim or emacs)
- Compile and verify the correct operation
- Submit your file using the course submission utility
- Message me in chat when you are done so I can verify

Phase 2: To be completed by the lab due date:
- "Wheel Game Bonus Round"
- Create a two-player game with the following functionality:
- Player 1 enters a crossword clue, and the answer
- Convert the answer to upper case.
- e.g.
```
Clue: "Where chemists mix chemicals or programmers learn to code"
Answer: "LABORATORY"
```

- Clear the terminal by printing some extra lines of whitespace
- Player 2 is shown the crossword clue, with the letters replaced with underscores and with spaces between the letters.
- Fill in the letters R S T L N E anywhere they appear in the clue, e.g.
```
Clue: "Where chemists mix chemicals or programmers learn to code"
Answer: "L _ _ _ R _ T _ R _"
```
- Player 2 then picks three more consonants and a vowel
    - Perform some rudimentary error checking here to ensure the correct number of letters were entered, and that the first three letters are consonants and the last letter is a vowel.
- Fill in the word with those letters included, as before, e.g.
```
Choose three consonants and a vowel: CBFO
Clue: "Where chemists mix chemicals or programmers learn to code"
Answer: "L _ B O R _ T O R _"
```
- Player 2 then gets three guesses to figure out the word.
- Inform the player if they guessed correctly after each guess, and if so, end the game.

Hints: You may wish to make use of the following utilities:
- Java `Scanner`
- Java `StringBuilder`
- String Class Functions, such as `.toUpperCase`, `.contains`, `.charAt`, `.equals`

---

## Submission

Assignments and labs for this course are submitted directly on Snoopy.

Here is how to submit phase 1:

Log into Snoopy. From Snoopy, submit your HelloWorld.java file using the following command:
```
~ense370/bin/submit L00 helloworld.java
```
You may check that you have submitted your assignment correctly by typing
```
~ense370/bin/submit -check
```

Please submit your phase 2 files to Snoopy. Include the java file(s) for the application, and include the command line calls in a `readme.txt` file for compiling and running your application.

Example commands in `readme.txt`:
```
javac WheelGame.java
java WheelGame
```

Submit phase 2 using the following command on Snoopy:

```
~ense370/bin/submit L01 WheelGame.java readme.txt
```

---

## References

K. Skoglund, [Unix Essential Training](https://www.lynda.com/Unix-tutorials/Work-Unix-from-command-line/2355069/3121230-4.html), Lynda from LinkedIn, 2020.


P.Heinlein, [Using ProxyJump with SSH and SCP](https://www.madboa.com/blog/2017/11/02/ssh-proxyjump/), madboa.com, 2017.


C. Collins, [SSH to remote hosts through a proxy or bastion with ProxyJump](https://www.redhat.com/sysadmin/ssh-proxy-bastion-proxyjump), Red Hat, 2019.
