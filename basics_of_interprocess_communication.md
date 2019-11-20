# Basics of Interprocess Comunication
**Eichhorn Moritz**

## Race Condition
A race condition occurs when two process try to access or change shared data. When this happens almost simultaneously the changes of the first process will be overwritten by the changes of the second process. So it's a race where only one process can win.

## Disabling Interrupts
### i. Why is it impossible to achieve Mutual Exclusion via disabling interrupts on a multi-core machine?

It's impossible, because a process, that wants to enter the critical region aswell, could run on another core and nothing stops this process from doing entering it. So these two processes could still enter the critical region at the same time.

### ii. Why is it dangerous to give user processes the power to disable interrupts?
Because, if this process for example has an infinite loop in it, this process will never leave the critical region and the scheduler won't be able to do anything, because there are no interrupts. So all other processes will be on "ready" and the whole system probably needs to be restarted. 

## Peterson's Solution
### i. Play through the two scenarios of the handout of Peterson's solution. Document how it works.
    Code of enter_region(): 

    int loser // shared variable
    Bool interested[2] // two processes only 

    void enter_region(int process)
    {
        int other = 1 - process;
        interested[process] = True;
        loser = process;
        while (loser == process && interested[other]) ;
    }

#### Scenario 1
    Process 0 calls enter_region(), finishes it, and is within the CR.
After that ```interested[0] = True``` and ```loser = 0```. Since ```interested[1] = false```
the process is allowed to enter the CR.

    Process 1 calls enter_region()
First the process sets ```interested[1] = True```, then ```loser = 1```. But because ```loser == process && interested[other]```
is true the process isn't allowed to enter the CR.

    Process 0 calls leave_region()
When process 0 calls leave_region() it sets ```interested[0] = false```.
So now process 1, which is always checking if ```loser == process && interested[other]``` is true, enters the CR, because the statement is False.

#### Scenario 2
    Process 0 and Process 1 go through the whole loop nearly simultaneously
When the two processes call enter_region() process 0 sets ```interested[0] = True``` and process 1 sets ```interested[1] = True```. Then one of the two process has to be the first to set ```loser = process``` (For this example let this be process 0). Right after that the other process (In our case now process 1) sets ```loser = process``` aswell. In our case ```loser``` is now 1. So process 0 can enter the critical region and process 1 is stuck in the while loop, until process 0 leaves the CR.

### ii. Play through the scenario which makes the strict alternation approach fail. Document how it fails.
    while (True) {                                       while (True) {
        while (turn != 0) ;                                 while (turn != 1) ;
        critical_region();                                  critical_region();
        turn = 1;                                           turn = 0;
        noncritical_region();                               noncritical_region();
    }                                                    }

Let's assume: Process A has high priority, i.e., has big time slices  
Process B has low priority, i.e., has small time slices.

So let's say Process A gets the time to run through the whole loop and then the scheduler gives Process B the time to only do one step of the loop. Process A runs through the loop and sets ```turn = 1``` now Process B checks ```turn != 1``` which is false. So it goes to critical_region(). But now Process A gets it's time and checks wether ```turn != 0``` which is true. It checks this many times until Process B gets time to enter the CR. But then it gets it's time again JUST to check something that we know can't change within Process A's time. But Process A checks this over and over.

### iii. What is the meaning of the variable loser in Peterson's solution? When does it have any effect?
Loser is the ID of the last process that wants to enter the critical region. It has an effect when a process is already in the critical region and another process wants to enter it. The variable stops other processes from entering the CR, when one process is already in the CR.

### iv. Extend the given functions such they can handle three processes.

    int next = -1// shared variable
    int loser;
    Bool interested[3] // two processes only

    void enter_region(int process)
    {
        int other1;
        int other2;

        if(process == 0) {
            other1 = 1;
            other2 = 2;
        } else if(process == 1) {
            other1 = 0;
            other2 = 2;
        } else {
            other1 = 0;
            other2 = 1;
        }

        if(next = -1) {
            next = process
        }
        else {
            loser = process;
            next = -1;
        }       

        interested[process] = True;
        
        while ((process == loser || process == next) && (interested[other1] || interested[other2])) ;
    }

    void leave_region(int process)
    {
        interested[process] = False;
        loser = next;
        next = -1;
    }
    

