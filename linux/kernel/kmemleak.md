How to find memory leaks in kernel
==================================

Kmemleak provides a way of detecting possible kernel memory leaks in kernel modules. It reports the memleaks in /sys/kernel/debug/kmemleaks file. A similar method is used by the `Valgrind` tool (memcheck --leak-check) to detect the memory leaks in user-space applications.

### How to use

CONFIG_DEBUG_KMEMLEAK in "Kernel hacking" has to be enabled. A kernel
thread scans the memory every 10 minutes (by default) and prints the
number of new unreferenced objects found. To display the details of all
the possible memory leaks:

        # mount -t debugfs nodev /sys/kernel/debug/
        # cat /sys/kernel/debug/kmemleak

To trigger an intermediate memory scan:

        # echo scan > /sys/kernel/debug/kmemleak
    
In kmemleak file you will find the backtrack.
    
Note that the orphan objects are listed in the order they were allocated
and one object at the beginning of the list may cause other subsequent
objects to be reported as orphan.


### Basic Algorithm

The memory allocations via kmalloc, vmalloc, kmem_cache_alloc and
friends are traced and the pointers, together with additional
information like size and stack trace, are stored in a prio search tree.
The corresponding freeing function calls are tracked and the pointers
removed from the kmemleak data structures.

An allocated block of memory is considered orphan if no pointer to its
start address or to any location inside the block can be found by
scanning the memory (including saved registers). This means that there
might be no way for the kernel to pass the address of the allocated
block to a freeing function and therefore the block is considered a
memory leak.

The scanning algorithm steps:

1. mark all objects as white (remaining white objects will later be considered orphan)
2. scan the memory starting with the data section and stacks, checking the values against the addresses stored in the prio search tree. If a pointer to a white object is found, the object is added to the gray list
3. scan the gray objects for matching addresses (some white objects can become gray and added at the end of the gray list) until the gray set is finished
4. the remaining white objects are considered orphan and reported via /sys/kernel/debug/kmemleak



[SOURCE]

<http://lxr.free-electrons.com/source/Documentation/kmemleak.txt>
<http://lwn.net/Articles/187979/>