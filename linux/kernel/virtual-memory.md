Virtual Memory in the Linux Kernel
==================================

Linux processes execute in a virtual environment that makes it appear as if each process had the entire address space of the CPU available to itself. This virtual address space extends from address 0 all the way to the maximum address. On a 32-bit platform, such as IA-32, the maximum address is 2^32 - 1 or 0xffffffff. On a 64-bit platform, such as IA-64, this is 2^64 -1 or 0xffffffffffffffff.

Reasons for using virtual memory

1. Resource virtualization
2. Information isolation
3. Fault isolation


### Introduction to the Virtual Memory System

![Fig 1](../imgs/address-space.png)

The virtual address space is divided into equal-sized pieces called virtual pages. Virtual pages have a fixed size that is an integer power of 2. For example, IA-32 uses a page size of 4 Kbytes. To maximize performance, IA-64 supports multiple page sizes and Linux can be configured to use a size of 4, 8, 16, or 64 Kbytes.

#### Virtual-to-physical address translation

Processes are under the illusion of being able to store data to virtual memory and retrieve it later on as if it were stored in real memory. In reality, only physical memory can store data. Thus, each virtual page that is in use must be mapped to some page frame in physical memory. 


                         MMU
     virtual/   +----------------------+
        logical |  +----------------+  | logical is Intel terms
        ------->|  | Segmented Unit |  |
                |  +----------------+  |
                |          | linear    | 
                |  +----------------+  |
                |  | Paging Unit    |  |
                |  +----------------+  |
                |          |           |
                +----------|-----------+
                           | physical
                           |
                           v

In order to access *any* physical pages, that page *must* be in the process's page table - every process has its own page table. That is the base.

First, even it is often said a process is "given" a unique 4GB virtual address space, it doesn't really mean that the process can do whatever it wants in that space: to access a paricular area inside that virtual space, it must ask kernel for a so-called "valid" memory area for it - the corresponding data structure defined in Linux is known as "vm_area_struct" or VMAs. You can check all VMAs associated with a process through "pmap" command on a process id.

A second point is that even in theory, a process got the "potential" of
accessing 4GB space, but say a user-space application makes use of libc, then libc should be mapped to the virtual space; the user-space application may also make use of syscalls, that means kernel will work on behalf of this process, so kernel image should also be mapped to process's virtual address space: and this mapping is better be permanent, given how often a process needs to switch to kernel mode. A temporary mapping scheme seems possible, but doesn't make much sense.

To summarize, a 4GB virtual address space for a process needs to be split
between kernel and user space program: therefore the well-known 3G/1G split.
User space takes the 0-3GB, and kernel takes the 3GB-4GB.

Thus, in the 3G/1G split, kernel has the virtual address space of 1GB.
**Remember that to access a physical address, you need a virtual address to start with, even for kernel.** So if you don't do anything special, the 1GB virtual address effectively limits the physical space a kernel can access to 1GB. Okay, maybe this is a third less obvious detail: kernel _needs_ to access *every* physical memory to make full use of it.


In the early days, where a machine's physical space is much less than 1GB, it is OK, the whole physical memory is mapped to this 1GB virtual address.

       process address space 

    4GB +---------------+
        |     512MB     |
        +---------------+ <------+     physical memory               
        |     512MB     |        | 
    3GB +---------------+ <--+   +---> +------------+ 
        |               |    |         |   512 MB   |
        |     /////     |    +-------> +------------+
        |               |     
    0GB +---------------+    
    
    
Example: Physical address {x} is mapped in kernel address space, virtual address is {PAGE_OFFSET + x}, where PAGE_OFFSET is defined as 3GB in Linux kernel for the 3/1 split.

Two general observations related to this:

- When all physical memory can be directly mapped into virtual address space, those corresponding virtual addresses are also called "kernel logical address". These logical address can often be mapped to physical address through constant offset, 3GB for example.

- The part of physical memory can be mapped into virtual space is also known as "Low Memory", conversely, those can't be mapped (say the portion over 1GB) is known as High Memory. In above case, all 512MB is Low Memory. 

Now you say, most machines got 2GB or more physical memory now. What happens then? when you want to access physical memory above 1GB (or more precisely, 896MB), Linux kernel use 128 MB virtual address space to *temporarily* map those virtual addresses to the physical address, thus to achieve the goal of being able to access all physical pages. The following figure roughly illustrate the scheme:

                                          physical mem
       process address space    +------> +------------+
                                |        |  3200 M    |
                                |        |            |
    4GB +---------------+ <-----+        |  HIGH MEM  |
        |     128 MB    |                |            |
        +---------------+ <---------+    |            |
        +---------------+ <------+  |    |            | 
        |     896 MB    |        |  +--> +------------+         
    3GB +---------------+ <--+   +-----> +------------+ 
        |               |    |           |   896 MB   |
        |     /////     |    +---------> +------------+
        |               |     
    0GB +---------------+     






[SOURCE]

<http://www.informit.com/articles/article.aspx?p=29961>
<http://users.nccs.gov/~fwang2/linux/lk_addressing.txt>