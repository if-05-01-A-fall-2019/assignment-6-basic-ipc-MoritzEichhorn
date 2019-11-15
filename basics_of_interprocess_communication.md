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
