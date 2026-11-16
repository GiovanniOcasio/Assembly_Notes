## What is the CPU

The CPU is the brain of the computer and it performs one task, read instructions from memory and executes them, one by one.

Inside the CPU are `registers` that hold a single number and can be accessed by the CPU faster than accessing RAM.

The CPU also has a special register called the Program Counter, which holds the memory address of the next instruction. Once the instruction has been executed, the PC moves to the next address automatically.

---
## Memory

CPU instructions are stored in memory (AKA Random Access Memory). Memory is like a giant grid of numbered slots, each slot has a unique address and can hold one byte of data (0-255).

The CPU can request the data at an address and can also overwrite the data at an address.

---
## Memory vs Registers

Registers are accessed by name `rax`, `rbx`, `rcx`, etc. Memory is accessed by address.

An instruction can use at most one memory operand. To combine two values that are both in memory, load one of them into a register first. If you try to add two numbers that are both in memory, the assembler will give you an error. The CPU cannot do it. You must load one of them into a register first.

Registers are faster than memory and where the CPU keeps values it is working with.

| Step | What Happens | Example |
|------|--------------|----------|
| LOAD | Copy data from memory into a register | move rax, [count] 
| WORK | Do something with the register | add rax |
| STORE | Copy the results back to memory (if needed) | mov [results], rax |

---
## How Do Instructions Work?

Human-readable text, such as assembly code, is converted into machine readable code known as **instruction code**

The Program Counter (AKA the Instruction Pointer) is a special register that holds the memory address of the next instruction to execute.

The CPU Repeats these three steps forever:

| Step | What Happens |
|------|--------------|
| FETCH | Reads the instruction at the address in the PC |
| EXECUTE | Perform that instruction |
| ADVANCE | Move PC to the next instruction |

Every instruction has two parts:

| Part | What it does | Example |
|------|--------------|---------|
| Opcode | What to DO | `mov`, `add`, `cmp` |
| Operand | What to do it TO | `rax`, `5`, `[result]` |

---
## Two-File Pattern

We will use two separate file, the `main.asm` and the `data.asm`

| File | Purpose |
|------|---------|
| main.asm | Stores all code (instructions, logic, and flow) |
| data.asm | Stores all data (strings, numbers, variables) |

---
## Converting Decimal to Binary

Simple method divide by 2 repeatedly and read the remainer backwards:

| Step | Division | Result | Remainder |
|------|----------|--------|-----------|
| 1 | 13 / 2 | 6 | 1 |
| 2 | 6 / 2 | 3 | 0 |
| 3 | 3 / 2 | 1 | 1 |
| 4 | 1 / 2 | 0 | 1 |

The binary of 13 is 1101

---
## Hex and Converting Hex to Decimal

Hexadecimal uses 16 symbols:

| Digit | Value |
|-------|-------|
| 0-9 | 0 to 9 |
| A | 10 |
| B | 11 |
| C | 12 |
| D | 13 |
| E | 14 |
| F | 15 |

Hex represents 4 bits.

Hex is commonly used in assembly to:
* Represent Memory Address
* ASCII codes
* Register values in debuggers
* Color values

Converting Hex to Decimal involves multiplying each character by a power of 16. Review the table:

| Position | Power of 16 | Decimal Value |
|----------|-------------|---------------|
|Right most | 16^0^ | 1 |
| Next | 16^1^ | 16 |
| Next | 16^2^ | 256 |
| Next | 16^3^ | 4096 |

For example, if we have a hex value 0x3E8:

| Position | Hex Digit | Place Value | Calculation |
|----------|-----------|-------------|-------------|
| Left | 3 | 256 | 3 x 256 = 768 |
| Middle | E | 16 | 14 x 16 = 224 |
| Right | 8 | 1 | 8 x 1 = 8 |
| Total | | |768 + 224 + 8 = 1000 |

---
## Converting Decimal to Hex 

Now instead of dividing by 2 you divide by 16 and read the remainder backwards.

In the example below we have a decimal value of 100 that we're going to convert into hex:

| Step | Division | Result | Remainder |
|------|----------|--------|-----------|
| 1 | 1000 / 16 | 62 | 8 |
| 2 | 62 / 16 | 3 | 14 |
| 3 | 3 / 16 | 0 | 3 |

The hex value is 0x3E8

---
## ASCII & Hex

ASCII is the format with which computers store letters.

Important ASCII Values to Memorize:

| Character | Hex | Why You Need It |
|-----------|-----|-----------------|
| A | 0x41 | Uppercase Letters Start Here |
| a | 0x61 | Lowercase Letters Start Here | 
| 0 | 0x30 | Digits Start Here |
| Space | 0x20 | Separating Words |
| Newline (\n) | 0x0A | Moving to Next Line | 
| Null (\0) | 0x00 | End of String Marker |

The `main.asm` file will include all the content of the `data.asm` using the `%include` directive.

---
## What is MOV?

The `MOV` instruction is the most fundamental and frequently used instruction in assembly language. It copies data from one location to another:

### Basic Syntax
```
mov destination, source
```

MOV copies the data from the source to the destination, the source remains unchanged.

```
mov rax, 60
```

Here the value of `60` was copied into the `rax`

---
## MOV Immediate

The simplest form of MOV is MOV immediate, where the value is written directly in the instruction itself. The is no output yet but the number is successfully put into registers.

## MOV Register to Register

Similar to moving set values, we can also copy data from one register to another using the same format:

```
mov destination_register, source_register
```

## MOV Memory to Register

Similar to moving data from register to register we can move data from memory to register. We can do that using the following syntax:
```
mov register, [address]
```

The `[]` signifies that area of memory we want to copy the data from.

If memory at a particular address stores a byte it should be copied to the `al` register, which is an 8-bit (1 byte) register.

## MOV Register to Memory

This will be similar to the previous syntax except we will reference the address as the destination and the register as the source:

```
mov [address], register
```

---
## LEA - Load Address 

LEA - stands for Load Effective Address. It calculates a memory address and puts that address into a register, without going to memory.

Syntax:
```
lea register, [address]
```

In assembly, when you want to print a string or pass a memory location to a function, you need the **address**, not the value. The write syscall expects an address in `rsi`

---
## MOV with Different Sizes

| Register | Size | Bits | When to Use |
|----------|------|------|-------------|
| `rax` | 8 bytes | 64 bits | Full numbers, addresses, syscall numbers |
| `eax` | 4 bytes | 32 bits | Lower half of `rax` |
| `ax` | 2 bytes | 16 bits | Lower half of `eax` |
| `al` | 1 byte | 8 bits | Lower half of `ax` (bits 0-7) |
| `ah` | 1 byte | 8 bits | Upper half of `ax` (bits 8-15) |

If there is a size mismatch, for example if you write 8 bits to a larger register on the first 8 bits will be changed.

---
## Swap Registers

When you need to swap values between two registers you require a third register as a temporary register. For example:

```
mov rcx, rax ; We are copying the value of rax to rcx our temporary register
mov rax, rbx ; We are copying rbx value to rax
mov rbx, rcx ; We are copying rcx to rbx completing our register swap
```
---
## Data Definition

The `.data` section is a part of your assembly program where you define data that will be stored in memory before your program runs.

The .data section allows you to create numbers, strings, and variables. It also allows you to use labels and load/store data you  create.

A simple example:
```
section .data
	age db 25
	price dw 1000
	name db 'Bob'
```

**Important Rules**

| Rule | Explanation |
|------|-------------|
| Keep your data together | You could open  `section .data` more tha once, but one section in `data.asm` is easier to read |
| Labels must be unique | No two labels can be the same |
| Data is created before your code runs | It is ready to use when `_start` begins |

---
## db - Define Byte 

The most basic way to create data in memory is `db`: Define Byte. It creates one byte of data.

Syntax:
```
	label db value
```

| What can be stored in one byte | Range | Example |
|--------------------------------|-------|---------|
| Small numbers | 0 - 255 | `db 100` |
| Character | A - z | `db 'X'` |
| ASCII code | 0 -255 | `db 65` |

You can also store multiple bytes in a row using commas:

```
section .data
	numbers db 5, 10, 15, 20
	letters db 'A', 'B', 'C'
```
Important rules:

| Rule | Explanation |
|------|-------------|
| Value must fit in 1 byte | 0 - 255 or a single byte character |
| Use an 8-bit register to load or store | `al/bl/cl/dl` |


## Strings with db

`db` can also define a string: multiple bytes in a row

Syntax:
```
	label db 'text here'
```

Example:
```
section .data
	message db 'Hello'
```

Adding a newline requires the addition of `0x0a`

Example:
```
section .data
	greeting db 'Hello', 0x0a
	farewell db 'Goodbye', 0x0a
```



---
## dw - Define Word

To store larger numbers you would use `dw`: Define Word. A word in x86 assembly is equivalent to two bytes.

Syntax:
```
	label dw value
```

| What can be stored in a word | Range | Example |
|--------------------------------|-------|---------|
| Small to medium numbers | 0 - 65535 | `dw 32000` |
| Memory Offset | 0 - 65535 | `dw 0x7FFF` |

Use the `ax` register when working with `dw`

---
## dd - Define Double Word
 
To store up to four bytes of data you would use `dd`: Define Double Word

Syntax:
```
	label dd value
```

| Directive | Size | Range | Register to Use |
|-----------|------|-------|-----------------|
| dd | 4 bytes | 0 - 4,294,967,295 | `eax` |

---
## dq - Define Quad Word

To store up to 8 bytes if data you would use `dq`: Define Quad Word

Syntax:
```
	label dq value
```

| Directive | Size | Range | Register to Use |
|-----------|------|-------|-----------------|
| dq | 8 bytes | Very large 2^64^ - 1 | `rax` |

`dq` is also used to store memory addresses (pointers):

```
section .data
    message db 'Hello', 0
    pointer dq message      ; Stores the address of message
```

---
## The $ Symbol


When printing strings with the write syscall you need to tell the CPU how many bytes to print. the `$` symbol automates this calculation

Example:
```
section .data
    first db 'Hi', 0x0a
    len1 equ $ - first      ; len1 = 3 ('H','i', newline)
    second db 'Hello', 0x0a
    len2 equ $ - second     ; len2 = 6 ('H','e','l','l','o', newline)
```
---
## Exit Syscall

`Syscall` is a request from your program to the operating system.

| Task `syscall` can perform | Task the CPU can perform |
|----------------------------|--------------------------|
| Print Text to Screen | Add two numbers |
| Read Keyboard Input | Move data between registers |
| Exit a program | |
| Open a file | |

**How `syscall` Works**
* You put a syscall number in `rax`
* You put arguments in other registers
* You execute the syscall instruction

| Syscall | Number | What It Does | 
|---------|--------|--------------|
| `exit` | 60 | Ends the program |
| `write` | 1 | Prints text to screen |

---
## Exit Syscall (60)

A little more detail:

`rax` with a value of 60 signifies that the CPU should access the Linux kernel. Once the linux kernel inspects the register containing the exit code (in this scenario `rdi`), it will exit with a code 0 (the program completed successfully)
```
mov rax, 60
mov rdi, 0
syscall
```

You can check the programs exit code with the following command
```
echo $?
```

---
## Exit Codes

You must set `rdi` before the exit syscall. If you don't it'll contain what ever was previously stored in `rdi`.

The following is an example of a complete program that doesn't perform any task:
```
section .text
    global _start

    _start:
    mov rax, 60
    mov rdi, 0
    syscall
```

---
## Write Syscall

The `write` syscall tells the operating system to print text to the screen or write to a file. The syscall number for write is `1`.

Unlike the exit syscall the write syscall requires three arguments:

| Argument | Register | What is Does | Example |
|----------|----------|--------------|---------|
| File Descriptor | `rdi` | Where to write (1 = screen) | `mov rdi, 1` |
| Buffer Address | `rsi` | Where the test is in memory | `mov rsi, memory` |
| Byte count | `rdx` | How many bytes to print | `mov rdx, 13` |

```
mov rax, 1      ; syscall number for write
mov rdi, 1      ; file descriptor 1 = stdout (screen)
mov rsi, msg    ; address of the string to print
mov rdx, 13     ; number of bytes to print
syscall         ; ask the OS to print
```

---
## File Descriptor (rdi)

There are 3 standard file descriptors in Linux:

| File Descriptor | Name | Where the data goes |
|-----------------|------|---------------------|
| 0 | stdin | Keyboard input (reads user input) |
| 1 | stdout | Screen output (normal program output) |
| 2 | stderr | Screen output (error message) |

---
## Buffer Address (rsi)

After telling the OS where to send the output, you need to tell it what to output. You do this by passing the address of the string in memory.

The register for the buffer (block of memory that holds data) address is `rsi`. The write syscall will read bytes from that address until it reaches the length specified in the `rdx`

If you do not set the `rsi`, the OS will try to read what was previously stored there.

---
## Byte Count (rdx)

Since the OS does not know where the string ends we need to specify a length, without a length the OS would keep reading memory forever.

---
## Multiple Lines

Multiple lines can be written by writing two separate syscalls for each value.

Before the second write syscall, you must load `1` into `rax` again, because the first `syscall` leaves its return value in `rax`.
