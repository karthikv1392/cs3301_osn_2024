---
layout: page
title: Mini Project 1
permalink: /mini-projects/mp1
parent: Mini Projects
nav_order: 2
---

# Mini Project 1 : Building your own shell

Before starting, make sure to read this document **completely** including **Instructions,  Guidelines,  Plagiarism  Policy and Submission  Guidelines** before asking any doubts as they might already be covered here.  Additionally, you are recommended to read these to avoid unnecessary penalties.

**Note that Part B of the Mini Project will be released on 17th August, 2024 and the GitHub Classroom link for this Mini Project will also be released along with it.**

  

##  Instructions

  

In  this  Mini  Project, you will be building your own shell using C.  As the scale of code will be very large, it is **necessary to write modular code.  Do  NOT write monolithic code.** Following is the definition of what modular code should look like : your codebase must be separated in different C files based on the tasks that it is intended to perform.  Create header files to include headers from  C library.  You will be penalized heavily if your code is non modular.

  

It is highly recommended to start this mini project early to complete it in time as it will be quite intensive.  There are 2 parts to this assignment,  with each of them having their own deadlines before the single hard deadline.  You are required to submit your code 2 times in the duration of  this assignment with the last deadline being a hard deadline.

  

-  1st deadline :  Only part A is required to be submitted

50% points of  Part  A will be given based on this submission.

-  2nd deadline :  Both  Parts  A and B must be submitted.

50% points of  Part  A and 100% points of  Part  B will be given based on this submission.

  

For easier understanding,  let’s say Part  A is worth 100 points total.  If your submission is worth 20 points from  Part  A during 1st deadline, and you submit it completely during 2nd deadline (100 points), you will get a total of  (20/2)  +  (100/2)  =  60 points.  If you submit Part  A completely in both deadlines, you will get full marks.  For  Part  B, whatever you submit during 2nd deadline will be used to award you points.

**Thus,  in  case  of missing 1st deadline,  50% points of  Part  A will be lost even if that part is submitted at a later deadline.**



  

##  Deadlines

  

**1st deadline :  11:59  PM  -  30  Aug,  2024**

  

**2nd deadline :  11:59  PM  -  13  Sep,  2024**

  

##  Part  A  :  Basic  System  Calls

  

###  Specification  1  :  Display  Requirement  [5]

  

On every line when waiting for user input, a shell prompt of the following form must appear along with it.  Do  NOT hard code the username/system name here.  The current directory address should be shown in the prompt as well.

  

```jsx

<Username@SystemName:~>

```

  

For example,  if system name is “SYS”, username is “JohnDoe”, and the user is currently in the home directory, then prompt looks like this  :

  

```jsx

<JohnDoe@SYS:~>

```

  

The directory from which shell is invoked becomes the home directory for the shell and represented with  “~”.  All paths inside this directory should be shown relative to it.  Absolute path of a directory/file must be shown when outside the home directory.

  

When user changes the working directory, the corresponding change in path of directory must be reflected in the next prompt.  For example, on going to the parent directory of the home directory of shell, following form of prompt is expected :

  

```jsx

<JohnDoe@SYS:/home/johndoe/sem3>

```

  

###  Specification  2  :  Input  Requirements  [5]

  

Keep  in mind the following requirements for input when implementing your shell :

  

-  Your shell should support a ‘;’ or ‘&’ separated list of commands.  You can use '**strtok**' to tokenize the input.

-  Your shell should account for random spaces and tabs when taking input.

-  The  “;” command can be used to give multiple commands at the same time.  This works similar to how “;” works in  Bash.

-  '&' operator runs the command preceding it in the background after printing the process id of the newly created process.  **(Refer to Specification 4 for more details on this)**

  

```jsx

./a.out

<JohnDoe@SYS:~> vim &

[1] 35006

<JohnDoe@SYS:~> sleep 5 & echo "Lorem ipsum"

[2] 35036

Lorem ipsum

# sleep runs in the background while echo runs in the foreground

<JohnDoe@SYS:~> hop test ; pwd

sleep with pid 35036 exited normally # after 5 seconds

~/test

<JohnDoe@SYS:~/test>

```

  

If any command is erroneous, then error should be printed.

  

```jsx

<JohnDoe@SYS:~> sleeeep 6

ERROR : 'sleeeep' is not a valid command

```

  

###  Specification  3  : hop [5]

  

‘hop’ command changes the directory that the shell is currently in.  It should also print the full path of working directory after changing.  The directory path/name can be provided as argument to this command.

  

-  You are also expected to implement the “.”,  “..”,  “~”, and “-” flags in hop.  ~ represents the home directory of shell (refer to specification 1).

-  You should support both absolute and relative paths, along with paths from home directory.

-  If more than one argument is present, execute hop sequentially with all of them being the argument one by one (from left to right).

-  If no argument is present, then hop into the home directory.

  

```jsx

<JohnDoe@SYS:~> hop test

/home/johndoe/test

<JohnDoe@SYS:~/test> hop assignment

/home/johndoe/test/assignment

<JohnDoe@SYS:~/test/assignment> hop ~

/home/johndoe

<JohnDoe@SYS:~> hop -

/home/johndoe/test/assignment

<JohnDoe@SYS:~/test/assignment> hop .. tutorial

/home/johndoe/test

/home/johndoe/test/tutorial

<JohnDoe@SYS:~/test/tutorial> hop ~/project

/home/johndoe/project

<JohnDoe@SYS:~/project>

```

  

**Note :**

  

-  You can assume that the paths/names will not contain any whitespace characters.

-  DON’T use ‘execvp’ or similar commands for implementing this.

  

###  Specification  4  : reveal [8]

  

‘reveal’ command lists all the files and directories in the specified directories in lexicographic order (default reveal does not show hidden files).  You should support the -a and -l flags.

  

-  -l : displays extra information

-  -a : displays all files, including hidden files

  

Similar to hop, you are expected to support “.”,  “..”,  “~”, and “-” symbols.

  

-  Support both relative and absolute paths.

-  If no argument is given, you should reveal at the current working directory.

-  Multiple arguments will not be given as input.

  

The input will always be in the format :

  

```jsx

reveal <flags> <path/name>

```

  

Handle the following cases also in  case  of flags :

  

- reveal -a <path/name>

- reveal -l <path/name>

- reveal -a -l <path/name>

- reveal -l -a <path/name>

- reveal -la <path/name>

- reveal -al <path/name>

- **Handle the cases where there are multiple  l & a like:-**
    - reveal -lala <path/name>
  

**Note :**

  

-  You can assume that the paths/names will not contain any whitespace characters.

-  DON’T use ‘execvp’ or similar commands for implementing this.

-  Use specific color coding to differentiate between file names, directories and executables in the output [green for executables, white for files and blue for directories].

-  Print a list of file/folders separated by newline characters.

-  The details printed with  -l should be the same as the ls command present in  Bash.

  

###  Specification  5  : log commands [8]

  

**log**

  

Implement a ‘log’ command which is similar to the actual history command in bash.  The  default number of commands it should store and output is 15  (max).  You must overwrite the oldest commands if more than the set number of commands are entered.  You should track the commands across all sessions and not just one.

  

Note  :

  

-  DO  NOT store a command in log if it is the exactly same as the previously entered command.

-  Store only valid commands (do not store erroneous commands).

-  Store the arguments along with the command

-  Do  NOT store the log command in log.

  

**log purge**

  

Clears all the log currently stored.  Do not store this command in the log.

  

**log execute \<index>**

  

Execute the command at <index> position in log (ordered from most recent to oldest).  If it’s the most recent command, don’t store it, otherwise store the command that was executed in log.

  

```jsx

<JohnDoe@SYS:~> reveal test

# output

<JohnDoe@SYS:~> sleep 5

# output

<JohnDoe@SYS:~> sleep 5

# output

<JohnDoe@SYS:~/test> echo "Lorem ipsum"

# output

<JohnDoe@SYS:~> log

reveal test

sleep 5

echo "Lorem ipsum"

<JohnDoe@SYS:~> log execute 1

# output of echo "Lorem ipsum"

<JohnDoe@SYS:~> log

reveal test

sleep 5

echo "Lorem ipsum"

<JohnDoe@SYS:~> log execute 3

# output of reveal test

<JohnDoe@SYS:~> log

reveal test

sleep 5

echo "Lorem ipsum"

reveal test

<JohnDoe@SYS:~> log purge

<JohnDoe@SYS:~> log

<JohnDoe@SYS:~>     //outputs nothing since log has been purged

```

  

###  Specification  6  :  System commands [12]

  

Your shell must be able to execute the other system commands present in  Bash as well like emacs, gedit etc.  This should be possible in both foreground and background processes.

  

**Foreground  Process**

  

Executing a command in foreground means the shell will wait for that process to complete and regain control afterwards.  Control  of terminal is handed over to this process for the time being while it is running.

  

Time taken by the foreground process and the name of the process should be printed in the next prompt if process takes >  2 seconds to run.  Round the time down to integer before printing in prompt.

  

```jsx

<JohnDoe@SYS:~> sleep 5

# sleeps for 5 seconds

<JohnDoe@SYS:~ sleep : 5s>

```

  

**Background  Process**

  

Any command invoked with  "&" is treated as a background command.  This implies that
your shell will spawn that process but doesn’t hand the control of terminal to it.  Shell will keep taking other user commands.  Whenever a new background process is started, print the PID  of the newly created background process on your shell also.

  

```jsx

<JohnDoe@SYS:~> sleep 10 &

13027

<JohnDoe@SYS:~> sleep 20 & # After 10 seconds

Sleep exited normally (13027)

13054

<JohnDoe@SYS:~> echo "Lorem Ipsum" # After 20 seconds

Sleep exited normally (13054)

Lorem Ipsum

```

  

**Note :**

  

-  No need to handle background processes for any commands implemented by yourself (hop, reveal, log etc.)

-  You should be able to run multiple background processes.

-  Whenever background process finishes, display message to user (after user enters any command, display all ended between last command run and this). **This should come autonomously after the bg process has finished without any interaction with user.**
-  Print process name along with pid when background process ends.  Also mention if the process ended normally or abnormally.

  

###  Specification  7  : proclore [5]


proclore is used to obtain information regarding a process.  If an argument is missing, print the information of your shell.

  

Information required to print :

  

- pid

-  Process  Status  (R/R+/S/S+/Z)

-  Process group

-  Virtual  Memory

-  Executable path of process

  

```jsx

<JohnDoe@SYS:~> proclore

pid : 210

process status : R+

Process Group : 210

Virtual memory : 167142

executable path : ~/a.out

  

<JohnDoe@SYS:~> proclore 301

pid : 301

process Status : R

Process Group : 243

Virtual memory : 177013

executable Path : /usr/bin/gcc

```

  

Process states :

  

-  R/R+  :  Running

-  S/S+  :  Sleeping  in an interruptible wait

-  Z  :  Zombie

  

The  “+” signifies whether it is a foreground or background process.

  

###  Specification  8  : seek [8]

  

‘seek’ command looks for a file/directory in the specified target directory (or current if no directory is specified).  It returns a list of relative paths (from target directory)  of all matching files/directories **(files in green and directories in blue)** separated with a newline character.

  

Note that by files, the text here refers to non-directory files.

  

**Flags :**

  

-  -d :  Only look for directories (ignore files even if name matches)

-  -f :  Only look for files (ignore directories even if name matches)

-  -e :  This flag is effective only when a single file or a single directory with the name is found.  If only one file (and no directories) is found, then print it’s output.  If only one directory (and no files) is found, then change current working directory to it.  Otherwise, the flag has no effect.  This flag should work with  -d and -f flags.

If  -e flag is enabled but the directory does not have access permission (execute) or file does not have read permission, then output **“Missing permissions for task!”**

  

**Argument  1 :**

  

The target that the user is looking for.  A name **without whitespace characters** will be given here.  You have to look for a file/folder with the exact name as this.

  

**Argument  2  (optional) :**

  

The path to target directory where the search will be performed (this path can have symbols like . and ~ as explained in the reveal command).  If  this argument is missing, target directory is the current working directory.  The target directory’s tree must be searched (and not just the directory).

  

```jsx

<JohnDoe@SYS:~> seek newfolder

./newfolder

./doe/newfolder

./doe/newfolder/newfolder.txt

<JohnDoe@SYS:~> seek newfolder ./doe

./newfolder # This is relative to ./doe

./newfolder/newfolder.txt

<JohnDoe@SYS:~> seek -d newfolder ./doe

./newfolder

<JohnDoe@SYS:~> seek -f newfolder ./doe

./newfolder/newfolder.txt

<JohnDoe@SYS:~> seek newfolder ./john

No match found!

<JohnDoe@SYS:~> seek -d -f newfolder ./doe

Invalid flags!

<JohnDoe@SYS:~> seek -e -f newfolder ./doe

./newfolder/newfolder.txt

This is a new folder! # Content of newfolder.txt

<JohnDoe@SYS:~> seek -e -d newfolder ./doe

./newfolder/

<JohnDoe@SYS:~/doe/newfolder>

```

  

Print  “**No match found!**”  in  case no matching files/directories is found.  Note that -d and -f flag can’t be used at the same time and must return error message “**Invalid flags!**”.

  

A call to this command will always be in the format :

  

**seek \<flags>  \<search>  \<target_directory>**

#  Guidelines

1.  The submission must be done in  C.  No other languages are allowed.

2.  All  C standard library functions are allowed unless explicitly mentioned.  You can find the list of header files in  C standard library here : https://en.cppreference.com/w/c/header

3.  Third party libraries are not allowed.

4.  If there is an error while running a command, it **should not cause the shell to crash**.  Handle errors as they are mentioned in the Mini  Project document.  If an **error is not mentioned here, you should still handle it appropriately**.  Look at perror.h for handling error routines.

5.  Do error handling for both user defined and system commands.

6.  Use specific color coding for error messages and prompts (You can choose the colors)

7.  As mentioned in  Input  Requirements, there can be multiple random tabs and spaces, you should handle these (and still run the entered command).

8.  The user can execute other files from within the shell (including the shell).  Your program must be able to execute them or print error if it is unable to do so.

9.  Use  of popen, pclose and system() calls is not permitted.

10.  Use the exec family of command to execute the system commands.  Errors should be handled appropriately.

11.  You are not required to implement background functionality for user commands (commands implemented by you)  in  this shell (hop, reveal, seek, proclore etc.)

12.  However, piping and redirection must work with both user and system commands.

13.  Use signal handlers to handle signals when switching between foreground and background or when a background process exits.

14.  Your code MUST compile for any marks to be given.  Use version control to save working versions of code before messing around with it and write modular code.

15.  Segmentation faults and other crashes while your shell is running will be penalized.

16.  The symbols <,  >,  >>,  &,  \|,  ;  ,  - would always correspond to their special meaning and would not appear otherwise, such as in inputs to echo etc.

17.  You are expected to implement everything except Specification  6 without using execvp.

  
# Useful commands/structs/files:
uname, hostname, signal, waitpid, getpid, kill, execvp, strtok, fork, getopt, readdir, opendir, readdir, closedir, getcwd, sleep, struct stat, struct dirent, /proc/interrupts, fopen, chdir, getopt, pwd.h (to obtain username), /proc/loadavg.

Also check out termios.h functions like tcgetattr(), tcsetattr() for terminal setting (raw mode/ cooked mode).

Refer to man pages on internet to learn of all possible invocations/variants of these general commands. Pay specific attention to the various data types used within these commands and explore the various header files needed to use these commands.
    
#  Plagiarism  Policy

**Do  NOT take code from your seniors or batchmates as we will do extensively evaluation for any plagiarism.**  Moss will be run against the previous few year’s submissions as well, and any case  of  **plagiarism will be punished heavily according to the course policy.**

  

There will be a viva conducted during manual evaluations, related to your code and the logic/concept involved in it.  You will get a penalty of up to 100%  if you are unable to answer these questions regarding the working of your code and the concept behind it.

  

Takeaway  :  Do not copy, it won’t get you marks but there is a high chance for it to get you trouble.

  

#  Submission  Guidelines

  

1.  Make a **makefile** for compiling all your code (with appropriate flags and linker options).  This makefile is expected to generate an **executable “a.out” on running the “make”** command.  Executing a.out should start your shell.

2.  Include a [README.md](http://README.md) describing which file corresponds to what part and any assumptions that you made.  Any assumptions not written in  README.md may not be considered during manual evaluation.

3. Submit modular code only.

4.  Following is the expected directory structure for your submission :

```

  

├── README.md

├── makefile

└── Other files and directories

```
