# Persistence Note

# persistence

Closer to the CPU, faster

CPU--Mem--Graphics--



### canonical protocol for programmed I/O

Interface: 

statue: busy or not

command

data

internal:

CPU

MEM

other chips



### Q

should the program running in kernel or user mode?

in kernel.

you don't want the user level program to touch the devices directly, since devices are shared bewteen processes. Device doesn't not have any protection from other processes. It can change the data on behalf of anyone.



### key features of programmed IO

CPU is involved every step of the way

A process invkes a system call, which results in device access

Under PIO, the process is spinning on the CPU waiting for the IO to complete





```
cons:

kernel involved everywhere

since devices are so slow compareing with cpu, cpu has to do a lot of unuseful spinning to do status check. just like what we do for the spinning lock.



pros:

simple and straight forward

cpu will keep checking so no time is wasted once the devices are available, so the responsive time is good.

```

### Scheduling Picture



### Other approach: interrupt

you don't need to keep pooling, the device will raise the exception to interrupt, and the handler will deal with it.



pro:

no wasting of cpu source

con:

if there are a lot of i/o devices, and they wake up one by one? the interrupts will happen consecutively.

So interrupt does have overhead.



#### canonical protocol for programmed io

very low latencty, it's watching. the moment it's ready, it leaves

#### interrupt 

need to  go wait and switch back. overhead always there. Latency

for some device it's ok, but not for everyone.



#### use programmed io if fast response is needed



Q:

if a process si running in the interrupt mode in a system call, and it goes to sleep. which mode is the process in? (running ready blocked?)

it's blocked.

instead in programmed io mode, the process is running.



### Further improvement: DMA

- even with interrupt, cpu is still involved in all data transfers
- Direct Memory Access allows devices to transfer data to/from memory without CPU involvement.
  - CPU sets up DMA, and context switch to run other process
  - when mem transfer finishes, interrupt raised
  - Kernel interrupt handler runs and wakes up the sleeping process



#### DMA uses kernel page table

there are some space reserved for dma so that kernel know where they are, just like how driver files are set.

the kernel page table can have physical mem 1-1 matched

#### scheduling pic



### Device Driver

- software dealing with devices
- part of kernel (a big portion)
- provide uniform abstraction for OS to interact with various types of devices



### Linux software stack regarding File System







#### Assuming we have 4 disks!!!

### RAID: redundant arrays of inexpensive disks

RAID0: no replication --> Striping

capacity: 4 * S bytes, where each disk store S bytes

performance: 

sequential read/write: 4 * times bandwidth

random read/write: same as snigle disk

reliability:  no improvement on reliability, any disk failure means the sys fails



RAID1: mirror data across two or more disks --> mirroring

RAID1-0: 

stripe of mirror -->  

0011

2233

4455

6677

mirror of strip -->

0101

2323

4545

6767

capacity:

performance: 2 * S

sequential read: 2 times since the disk head moving take time to skip blocks

sequential write: 2 * bandwidth as a single disk

random read: same as single

random write: write both copys at same time, about the same as single disk, slightly higher

Reliability: Tolerate one total disk failure



RAID4/5: split data across disks

bit-wise XOR all blocks in the same stripe, and store the result in the parity block

Can reconstruct any missing block from the others

RAID 4/5 differ on how parity blocks are distributed

read is like raid 0 with one fewer disk

write will additionly write the parity disk!

for example, if we only change 2, then we can change P0 by (old_p0 XOR old_2) XOR new_2



so To write one block:

read old data

read old p

write new data

write new p = (old_p0 XOR old_2) XOR new_2









RAID4

0123 P0

4567 P2

...

Performance:

the writing process is bottle neck



RAID5: rotating parity

p0 0 1 2 3

4 p1 5 6 7

...



Raid4/5:

capacity: 4 * S bytes

performance: 

sequential read: 4

sequential write: bottle necked by parity disk for raid 4, improve for raid5

Reliability: any single disk failure can be tolerated





### Unix File System

to understand the design principles of a very important file system

#### A few main considerations in Unix File sys Design

- provide a uniform structure for naming and accessing data objects throughout the system
- Enable data sharing between different programs
- Ensure consistency with concurrent access
- acheve good performance/reliability balance
  - hard drive is slow, buffer si important
  - care need to be taken to account for failure



#### Naming file

file are organized in a directory tree

a dir is also a file ---> mapping from name to inode

inode is the Unix sys internal data structure for a file

​	inode number uniquely identifies a file across the system





#### FIle sys Metadata

- Inode: stored in persistent storage, and cached in mem for faster access
- Directory: special file that contains mapping from names in that dir to their inode numbers



#### Operating on Files










