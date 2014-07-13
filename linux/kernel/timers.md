Timer Notes
===========
The hardware provides a system timer that the kernel uses to gauge the passing of time.This system timer works off of an electronic time source, such as a digital clock or the frequency of the processor.The system timer goes off (often called hitting or popping) at a preprogrammed
frequency, called the tick rate. When the system timer goes off, it issues an interrupt the kernel handles via a special interrupt handler.

The timer interrupt is important to the management of the operating system.A large number of kernel functions live and die by the passing of time. Some of the work executed periodically by the timer interrupt includes

* Updating the system uptime
* Updating the time of day
* On an SMP system, ensuring that the scheduler runqueues are balanced and, if not, balancing them (as discussed in Chapter 4,“Process Scheduling”)
* Running any dynamic timers that have expired
* Updating resource usage and processor time statistics

### The Tick Rate: HZ
The frequency of the system timer (the tick rate) is programmed on system boot based on a static preprocessor define, HZ.The value of HZ differs for each supported architecture.

The tick rate has a frequency of HZ hertz and a period of 1/HZ seconds. For example, by default the x86 architecture defines HZ to be 100.Therefore, the timer interrupt on i386 has a frequency of 100HZ and
occurs 100 times per second (every one-hundredth of a second, which is every 10 milliseconds). 

Increasing the tick rate means the timer interrupt runs more frequently. Consequently, the work it performs occurs more often.This has the following benefits:

* The timer interrupt has a higher resolution and, consequently, all timed events have a higher resolution.
* The accuracy of timed events improves.

#### Advantages with a Larger HZ

* Kernel timers execute with finer resolution and increased accuracy. (This provides a large number of improvements, one of which is the following.)
* System calls such as poll()and select()that optionally employ a timeout value
execute with improved precision.
* Measurements, such as resource usage or the system uptime, are recorded with a finer resolution.
* Process preemption occurs more accurately.

#### Disadvantages with a Larger HZ

* A higher tick rate implies more frequent timer interrupts, which implies higher overhead, because the processor must spend more time executing the timer interrupt handler.

*The higher the tick rate, the more time the processor spends executing the timer interrupt.This adds up to not just
less processor time available for other work, but also a more frequent thrashing of the processor’s cache and increase in power consumption.

### Jiffies
The global variable jiffies holds the number of ticks that have occurred since the system booted. On boot, the kernel initializes the variable to zero, and it is incremented by one during each timer interrupt.

The jiffies variable is declared in <linux/jiffies.h> as

        extern unsigned long volatile jiffies;


The following expression cona unit of jiffies:

        jiffies = (seconds * HZ)
        
Likewise, this expression converts from jiffies to seconds:

        seconds = (jiffies / HZ)

### Hardware Clocks and Timers
Architectures provide two hardware devices to help with time keeping: the system timer, which we have been discussing, and the real-time clock.

#### Real-Time Clock
The real-time clock (RTC) provides a nonvolatile device for storing the system time. The RTC continues to keep track of time even when the system is off by way of a small battery typically included on the system board. On the PC architecture, the RTC and the CMOS are integrated, and a single battery keeps the RTC running and the BIOS settings preserved.

On boot, the kernel reads the RTC and uses it to initialize the wall time, which is stored in the xtime variable.The kernel does not typically read the value again; however, some supported architectures, such as x86, periodically save the current wall time back to the RTC. Nonetheless, the real time clock’s primary importance is only during boot, when the xtime variable is initialized.

#### System Timer
The system timer serves a much more important (and frequent) role in the kernel’s timekeeping.The idea behind the system timer, regardless of architecture, is the same to provide a mechanism for driving an interrupt at a periodic rate.


