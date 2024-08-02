---
layout: page
title: Mini Project 0
permalink: /mini-projects/mp0
parent: Mini Projects
nav_order: 1
---

# Mini Project 0 : Intro to System Calls
Welcome everyone to your very first Mini Project in Operating Systems & Networks Course. This Mini Project is designed to give you a basic introduction to system calls and to give you an idea of what and how you will be working with in the coming Mini Projects.
A system call is a routine that allows a user application to request actions that require special privileges. Adding system calls is one of several ways to extend the functions provided by the kernel.

## GitHub Classroom

For this project, follow [this link](https://classroom.github.com/a/-UPXEXqx) to accept the assignment. You should find a list of roll numbers already available as soon as you click on the link, ensure that you select your roll number correctly as that will mapped to your github id throughout the course. 
You will then be assigned a private repository on GitHub. This is where you will be working on the mini project. All relevant instructions regarding this mini project can be found below.   

> **Note:** If you are not familiar with git, please go through the [git tutorial](https://rogerdudler.github.io/git-guide/) before proceeding.  
> **PS:** Your github id does not need to be with your college id, it can be with your personal email id as well.

## Instructions

This Mini Project works as a basic introduction to system calls and to give you an idea of what you will be working with in the coming Mini Projects. It is **highly recommended to go through the Mini Project** to make the following Mini Projects easier.

## Overview

This mini-project consists of two parts:
1. Creating a file manipulation program that accepts user input and displays file content.
2. Implementing a process creation program that demonstrates various process API calls.

### Part 1: File Management
Create a C program that does the following:

1. **File Creation on Startup**: The program should create a new file called `newfile.txt` with permissions set to `0644` on startup.
2. **INPUT Command**: The program should prompt the user to input a statement and append it to `newfile.txt` with a newline character (`\n`) at the end.
3. **PRINT Command**: The program should output the content of `newfile.txt` to the user.
  
Till **STOP** command is given, program should be able to take the input. When **STOP** is given the program should exit.
You can only use `printf` and `scanf` for taking user input and printing output. Commands like `fprintf` and `fscanf` are not allowed.

#### Sample Input and Output
```
INPUT -> Hello, World!
INPUT -> Welcome to C programming.
PRINT
Hello, World!
Welcome to C programming.
```


## Part 2: Process Creation and Management
Create a C program that does the following:

**Task 1**
- Before creating your first child, have the parent process access a variable (e.g., `x`) and set its value to 25. What is the value of the variable in the child process? What happens to the variable when both the child and parent change the value of `x`?

**Task 2**
- Create a child process that uses `exec()` to create a file named `newfile.txt` and store its parent process ID in the file.

**Task 3**
- Create a child process that outlives its parent process. The parent process should terminate immediately after forking. The child process should then print its parent process ID.
  
Compare the value of the parent process ID which is stored in the file and printed by the thrid child.What do you observe?

### Error Handling
- Ensure system calls are successful. If not, display an error message and exit the program.
- Ensure the file is successfully created and opened for writing. If not, display an error message.
- Handle invalid commands by prompting the user with an appropriate message.


## Submission

The submission for this mini project will be through GitHub Classroom. The codebase will be automatically downloaded at the deadline, so ensure that everything is up in time. No exceptions will be granted. Include any assumptions or instruction to run in README. 

MiniProject-0/  
├── Part-1/  
│     └── fileManagement.c  
├── Part-2/  
│     └── processManagement.c  
└── README.md  


Hard Deadline : 11:59 PM, 9th August 2023

The course policy regarding **late days is not applicable on Mini Project 0**, therefore you cannot use late days with Mini Project 0. If you use any AI tools don't forget to follow guidelines in the course website.