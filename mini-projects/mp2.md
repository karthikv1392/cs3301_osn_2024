---
layout: page
title: Mini Project 2
permalink: /mini-projects/mp2
parent: Mini Projects
nav_order: 3
---

# Mini Project 2 : Introduction to XV6 and Networking

## Deadline: 11th October 2024, 23:59 IST

## GitHub Classroom

Follow [this link](https://classroom.github.com/a/JsZCPDRN) to accept the assignment. You will then be assigned a private repository on GitHub. This is where you will be working on the mini project. All relevant instructions regarding this project can be found below.

## XV6

### System Calls \[20 points\]
#### 1. Gotta count 'em all \[7 points\]
Add the system call `getSysCount` and the corresponding user program `syscount`. `getSysCount`  counts the number of times a specific system call was called by a process and prints it. The system call to count is provided through the `syscount` program by the bits of an integer mask provided as `syscount <mask> command [args]` where `command [args]` is any other valid command in XV6.  

The specified `command [args]` runs till it exits, while the number of times the system call corresponding to the `<mask>` is called is counted by `syscount`. For example, to get the number of times the `i`th system call was called, the `mask` is specified as `1 << i`. You may assume that only 1 bit will be turned on (i.e. the number of times one specific syscall is called will be counted.) The enumeration of syscalls is provided in `kernel/syscall.h`.  

After the `command [args]` exits, the number of times the chosen syscall was called is printed out, along with its name in the following format: `PID <caller pid> called <syscall name> <n> times`. For example: 
```sh
$ syscount 32768 grep hello README.md
PID 6 called open 1 times.
$ 
```

Here `1 << 15 = 32678`, which corresponds to the `open` syscall and the pid of the process is 6.  

**NOTE**: The number of times the corresponding system call is called by the children of the process called with `syscount` should also be added to the same total. You may assume that we will count the number of times one syscall is counted and that a maximum of 31 system calls will exist at any point.  
#### 2. Wake me up when my timer ends \[13 points\]
In this specification you'll add a feature to xv6 that periodically alerts a process as it uses CPU time. This might be useful for compute-bound processes that want to limit how much CPU time they chew up, or for processes that want to compute but also want to take some periodic action. More generally, you'll be implementing a primitive form of user-level interrupt/fault handlers like a `SIGCHILD` handler.   

You should add a new `sigalarm(interval, handler)` system call. If an application calls `sigalarm(n, fn)` , then after every n  "ticks" of CPU time that the program consumes, the kernel will cause application function `fn`  to be called. When `fn`  returns, the application will resume where it left off.  

Add another system call `sigreturn()`, to reset the process state to before the `handler` was called. This system call needs to be made at the end of the handler so the process can resume where it left off.  

---
### Scheduling \[40 points]
The default scheduling policy in xv6 is round-robin-based. In this task, you’ll implement two other scheduling policies and incorporate them in xv6. The kernel should only use one scheduling policy  declared at compile time, with a default of round robin in case none are specified.  

Modify the `makefile` to support the `SCHEDULER` macro to compile the specified scheduling algorithm. Use the flags for compilation:-
- Lottery Based Scheduling: `LBS`  
- Multi Level Feedback Queue: `MLFQ`  
Your compilation process should look something like this: `make clean; make qemu SCHEDULER=MLFQ`.  

**Hints**:  
1. Use pre-processor directives to declare the alternate scheduling policy in `scheduler()` in `kernel/proc.h`.
2. Edit `struct proc` in `kernel/proc.h` to add information about a process.
3. Modify the `allocproc()` function to set up values when the process starts (see `kernel/proc.h`.)
#### 1. The process powerball \[15 points\]
Implement a preemptive lottery based scheduling policy that assigns a time slice to the process randomly in proportion to the number of tickets it owns. That is, the probability that the process runs in a given time slice is proportional to the number of tickets owned by it. You may use any method to generate (pseudo)random numbers.  

Implement a system call `int settickets(int number)` , which sets the number of tickets of the calling process. By default, each process should get one ticket; calling this routine makes it such that a process can raise the number of tickets it receives, and thus receive a higher proportion of CPU cycles. For example, a program can do the following to increase its tickets from the default of 1 to 2:
```
int newTicketNum = settickets(2);
if (newTicketNum == -1) {
	// changing tickets failed
	fprintf(2, "could not change tickets to 2 for process with pid %d\n", getpid());
}
```


This is the traditional lottery based scheduling policy, however, last time processes protested that coming early or late did not affect their winning chances. This time, if a process is considered the winner of the lottery, it is only if there are no other processes with the same number of tickets but an earlier arrival time.  
	Example: If there are three processes:  
		A, arrived at t=0s with 3 tickets,  
		B, arrived at t=3s with 4 tickets,  
		C, arrived at t=4s with 3 tickets,  
	and C is chosen as the winner at t=5s, the result is overturned and handed to A because it arrived earlier but has the same number of tickets.  

**Note**: You'll need to assign tickets to a process when it is created. Also, you'll need to make sure a child process starts with the same number of tickets as its parent. Only processes that are in the `RUNNABLE` state can participate in the lottery. The time slice is 1 tick. 
 
#### 2. MLF who? MLFQ! \[25 points\]
Implement a simplified preemptive MLFQ scheduler that allows processes to move between different priority queues based on their behavior and CPU bursts.  

- If a process uses too much CPU time, it is pushed to a lower priority queue, leaving I/O bound and interactive processes in the higher priority queues.  
- To prevent starvation, implement priority boosting.  

**Details:**  
1. Create four priority queues, giving the highest priority to queue number 0 and lowest priority to queue number 3  
2. The time-slice are as follows:
    1. For priority 0: 1 timer tick
    2. For priority 1: 4 timer ticks
    3. For priority 2: 8 timer ticks
    4. For priority 3: 16 timer ticks
    
    **NOTE:** Here tick refers to the clock interrupt timer. (see kernel/trap.c)  

Synopsis for the scheduler:-  
1.  On the initiation of a process, push it to the end of the highest priority queue (priority 0).
2. You should always run the processes that are in the highest priority queue that is not empty.
    Example:
    Initial Condition: A process is running in queue number 2 and there are no processes in both queues 1 and 0.  
    Now if another process enters in queue 0, then the current running process (residing in queue number 2) must be preempted and the process in queue 0 should be allocated the CPU.(The kernel can only preempt the process when it gets control of the hardware which is at the end of each tick so you can assume this condition)  
3. When the process completes, it leaves the system.
4. If the process uses the complete time slice assigned for its current priority queue, it is preempted and inserted at the end of the next lower level queue (except if it is already at the bottom queue, where it would be inserted at the end of the same queue.)
5. If a process voluntarily relinquishes control of the CPU(ex: for doing I/O operations), it leaves the queuing network, and when the process becomes ready again after the I/O operation, it is inserted at the tail of the same queue, from which it is relinquished earlier 
6. A round-robin scheduler should be used for processes at the lowest priority queue.
7. To prevent starvation, implement priority boosting:
    1. After a time period of 48 ticks, move all processes to the top most queue (priority 0)

**NOTE**  
`procdump`:  
This will be useful for debugging ( refer `kernel/proc.c` ). It prints a list of processes to the console when a user types `Ctrl-P` on the console. You can modify this functionality to print the state of the running process and display the other relevant information on the console.   

Use the `procdump` function to print the current status of the processes and check whether the processes are scheduled according to your logic. You are free to do any additions to the given file, to test your scheduler.   

---
### Report \[10 points\]
- The report must contain brief explanation about the implementation of the specifications. A few lines about your changes for each spec is fine.  
- Include the performance comparison between the default and 2 implemented scheduling policies by showing the average waiting and running times for processes. Set the processes to run on only 1 CPU for this purpose. Use the `schedulertest` command to get this information.  
- Answer the following in 3-4 lines, and optionally provide an example: What is the implication of adding the arrival time in the lottery based scheduling policy? Are there any pitfalls to watch out for? What happens if all processes have the same number of tickets?  

**MLFQ Analysis \[5 points\] (part of report)**
Create a timeline graph that shows which queue a process is in over time. Vary the length of time that each process consumes the CPU before willingly quitting using `schedulertest`. The graph should be a timeline/scatter plot between queue_id on the y-axis and time elapsed on the x-axis from start with color-coded processes. Ensure that the priority boost is visible in this graph. Below is a reference graph (note that it does not implement priority boost but aging, this is just for you to understand the format of the graph):  
[![Graph](https://github.com/karthikv1392/cs3301_osn/raw/7b2532a0c6455ca0cdc6ea1ad51088de69def407/mini-projects/graph.png)](https://github.com/karthikv1392/cs3301_osn/blob/7b2532a0c6455ca0cdc6ea1ad51088de69def407/mini-projects/graph.png)

## Networking
### 1. XOXO \[15 points\]
You are required to design and implement a simple multiplayer Tic-Tac-Toe game using networking concepts. The game will have a **server** to manage the game state and two **clients** (players) who will play against each other. 
#### **Overview**

- **Server**: The server will manage the game state (the Tic-Tac-Toe board), handle communication between the two players, and determine if there's a winner or if the game ends in a draw.
- **Clients**: Two clients will connect to the server and send their moves. The server will update the game board and broadcast the updated game state to both players.
#### **Game Rules**:

1. **The Board**:
    - The tic-tac-toe board is a 3x3 grid.
    - Players take turns to place their symbol ('X' or 'O') in an empty spot.
    - The game begins with an empty board, and the server will send this initial state to both players once the game starts.
2. **Starting the Game**:
    - The game will start when both players (clients) have connected to the server and confirmed they are ready to play.
    - The server will assign Player 1 to use 'X' and Player 2 to use 'O'.
3. **Game Flow**:
    - The game alternates between Player 1 and Player 2. The server has to ensure this.
    - Reject invalid moves and ask the player to try again.
    - Each player takes turns selecting a position on the grid by specifying the row and column number (e.g., 1 1 for the top-left corner).
    - After each move, the server updates the board and sends the current state of the board to both players. The clients should display it in a user-friendly manner.
    - The server also informs the next player that it’s their turn to make a move.
4. **Winning and Drawing**:
    - A player wins if they successfully place three of their symbols ('X' or 'O') in a row, column, or diagonal.
    - If the grid is full and no player has won, the game ends in a draw.
    - After a win or a draw, the server informs both players of the outcome, displaying the final board and the appropriate message:
        - "Player 1 Wins!"
        - "Player 2 Wins!"
        - "It's a Draw!"
5. **After the Game**:
    - Once the game ends, both players are asked if they would like to play again.
        - If both say yes, the server will reset the board and start a new game.
        - If both say no, the server closes the connection for both.
        - If one player says yes and the other says no, the player who wanted to continue is informed that their opponent did not wish to play, and the connection for both is closed.

- **You are expected to implement this twice: once using TCP sockets and once with UDP.**
### 2. Fake it till you make it \[15 points\]
We can't really ask you to implement the entire TCP/IP stack from scratch for about twenty marks (But, for the ones interested here's a repo on it - [https://github.com/saminiir/level-ip](https://github.com/saminiir/level-ip))  
In this specification, you will implement *some* TCP functionality using UDP.  

Functionalities that you have to implement are:  
1.  *Data Sequencing*: The sender (client or server - both should be able to send as well as receive data) must divide the data (assume some text) into smaller chunks (using chunks of fixed size or using a fixed number of chunks). Each chunk is assigned a number which is sent along with the transmission (use structs). The sender should also communicate the total number of chunks being sent \[1\]. After the receiver has data from all the chunks, it should aggregate them according to their sequence number and display the text.  
2.  *Retransmissions*: The receiver must send an ACK packet for every data chunk received (The packet must reference the sequence number of the received chunk). If the sender doesn't receive the acknowledgement for a chunk within a reasonable amount of time (say 0.1 seconds), it must resend the data. However, the sender shouldn't wait for receiving acknowledgement for a previously sent chunk before transmitting the next chunk \[2\].  

    \[1\] Regardless of whether you use a fixed number of chunks  
    
    \[2\] For implementation's sake, send ACK messages randomly to check whether retransmission is working - say, skip every third chunk's ACK. (Please comment out this code in your final submission)  
    
**Note**: Simulating a single client and a single server is sufficient.  
Submit your implementation for the server and client in `<mini-project2-directory>/networks/partB`  

You may use threads, but **you are not required to.** It can be done without them. (Hint: look up non-blocking sockets)

## [Guidelines](https://karthikv1392.github.io/cs3301_osn/course_policy/)

1. Do not change the basic file structure given on Github Classroom.
2. No deadline extensions will be granted.
3. We will use more than 2 CPUs to test for LBS. But for the MLFQ scheduler, we will only use 1 CPU.
4. Whenever you add new files do not forget to add them to the Makefile so that they get included in the build.
5. Make sure to include a **report, describing the implementation of your specifications, failing which will result in a direct 0 marking for those questions.**

**Do NOT copy from seniors or batch mates. We will rigorously evaluate cheating scenarios along with the previous few years' submissions.**

**A viva will be conducted during the evaluations, related to your code and also the logic/concepts involved. If you’re unable to answer them, you won't be awarded points for that feature/topic that you’ve implemented.**
