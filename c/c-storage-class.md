C Storage Classes
=================

A storage class defines the **scope (visibility)**, part of memory **where storage is allocated** and **life-time** of variables and/or functions within C Program. The are four storage classes in C are automatic, register, external, and static.

### Automatic
The **automatic** storage class is the default storage class for all local variables. 

        void myfunc() {
            auto int index;   // auto is default, you 
            int index;        // can skip it. 
        }
            
    

They are declared at the start of a block. Memory is allocated automatically upon entry to a block and freed automatically upon exit from the block. The scope of automatic variables is local to the block in which they are declared, including any blocks nested within that block.

### Register
It takes more time to read variables from memory. It is advisable to store  heavily used variables into machine's registers. Variables declared as **register** advises the compiler that these variables will be used heavily so better to store them in machine registers. But compilers are free to ignore the advise. The **register** declaration can only be applied to _automatic_ variables. 

        void myfunc() {
            register int index;   // Only applied to 
            ...                   // automatic variables 
        }

In practice, There are restriction on register variables. Only few variables may be kept in registers and only certain types are allowed.
It is not possible to take the address of register variables, regardless of whether the variable is actually placed in a register. Specific restriction on number and register types are hardware specific.

### Extern

**extern** is used to give a reference of a global variable that is visible to ALL the program files. When you use **extern** the variable cannot be initialized as all it does is point the variable name at a storage location that has been previously defined.

An external variable must be _defined_ exactly once, outside of any function. 

File 1: main.c
        
        int count=5;
        main()
        {
            write_extern();
        }
        
File 2: write.c

        void write_extern(void);
        extern int count;
        
        void write_extern(void)
        {
            printf("count is %i\n", count);
        }


### Static
A variable declared **static** within the body of a function maintains its value between function invocations.

A variable declared **static** within a module, (but outside the body of a function) is accessible by all functions within that module. It is not accessible by functions within any other module. That is, it is a localized global.

Functions declared **static** within a module may only be called by other functions within that module. That is, the scope of the function is localized to the module within which it is declared.





 