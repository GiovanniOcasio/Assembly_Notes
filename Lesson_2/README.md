## ADD and SUB

```
add destination, source
```

This increases the destination using the value of the source, while the source remains the same

```
sub destination, source
```

This will subtract the source from the destination, while the source remains the same.

---
## INC and DEC

`inc` adds one and `dec` subtracts one.

Ex:
```
mov rax, 6
inc rax ; Value has increased one
dec rax ; Value has decreased one
```

---
## Multiplying with IMUL

```
imul destination, source

mov rax, 15
mov rbx, 2
imul rax, rbx

; rax will now have a value of 30
```

This will multiply the destination by the source, while the source remains unchanged.

---
## Unsigned Division

```
div divisor
```

This will divide the unsigned 128-bit value in rdx:rax. In this scenario the quotient will be stored in the `rax` and the remainder will be stored in the `rdx`.

Ex: We must set the `rdx` to zero and we will be dividing the `rax` by the `rbx`

```
mov rax, 23
mov rdx, 0
mov rbx, 5
div rbx

; Quotient of 4 will be stored in the rax
; Remainder of 3 will be stored in the rdx
```
---
## CMP and the Zero Flag


`cmp` compares two values by determining the difference between the destination and the source. It does not store the value, but sets or clears specific bits in the `RFLAGS` or `EFLAGS` register based on the outcome.

```
mov rax, 12
mov rbx, 12
cmp rax, rbx
```

If the results yield a zero than the Zero Flag is set to 1 (`ZF = 1`)

---
## JE, JNE, and JMP

* `jmp` Always jumps tp the target label
* `je` Jumps if the Zero Flag is set (`ZF = 1`)
* `jne` Jumps if the Zero Flag is clear (`ZF = 0`)

```
	mov rax, 10
	mov rbx, 9
	cmp rax, rbx
	jne .done
	mov rax, 1
.done
	ret
```
```
	mov rax, 8
	mov rbx, 8
	cmp rax, rbx
	je .done
	mov rax, 1
.done
	ret
```

Conditional Example:
```
	mov rax, 20 ; Setting the intial value
	cmp r8, r9 ; Comparing two values
	jne .done ; If the two values do yield a remainder we'll execute the .done instruction
	mov rax, 10 ; If the two values result in a zero we'll skip jne and mov 10 into the rax
.done ; Instruction for the jne
	ret ; Returns the values
```

---
## Signed Comparisons

| Instruction | Meaning | Flag Condition Checked | When It Jumps (`cmp A, B` ) |
|-------------|---------|------------------------|-----------------------------|
| `jl` (Jump if Less) | Jump if A < B | SF != OF | A is strictly less than B |
| `jg` (Jump if Greater) | Jump if A > B | ZF = 0 and SF = OF | A is strictly less than B |
| `jle` (Jump if Less or Equal) | Jump if A <= B | ZF = 1 or SF != OF | A is less than or equal to B |
| `jge` (Jump if Greater or Equal | Jump is A >= B | ZF = 1 or SF = OF | A is greater than or equal to B |

* `ZF` (Zero Flag) Set to 1 if `A == B`
* `SF` (Signed Flag) Reflects the signed bit of the result (1 = negative, 0 = positive)
* `OF` (Overflow Flag) Set to 1 if signed arithemtic overflow occurred

Example:
```
	mov rax, 0
    cmp r8, r9 ; Compares r8 to r9
    jl .second ; If r8 is less than r9 jump to .second
    mov rax, r8 ; If r8 is greater mov it to the rax 
    sub rax, r9 ; Perform the subtraction to determine the absolute value
    jmp .done ; Jump to done function and output data
.second ; In this case r8 is less than r9 so this function is performed
    mov rax, r9 ; The larger r9 is mov to rax
    sub rax, r8 ; Perform the subtraction to get the absolute value 
.done
    ret
```

---
## Unsigned Comparisons

| Instruction | What it does | Flag Condition Checked |
|-------------|---------|------------------------|-----------------------------|
| `jb` (Jump if Below) | Jumps if the first operand is strictly less than the second | CF = 1 |
| `ja` (Jump if Above) | Jump if the first operand is strictly greater than the second | CF = 0 or ZF = 0 | 
| `jbe` (Jump if Below or Equal) | Jump if the first operand is less than or equal to the second | CF = 1 or ZF = 1 |
| `jae` (Jump if Above or Equal | Jump if the first operand is greater than or equal to the second operand | CF = 0 | 

* Unsigned refers to whole number data types that can only represent non-negative values
* `CF` (Carry Flag) Set to 1 if the CPU must borrow from an imaginary bit ( In a case like 5 - 10) or if adding two unsigned integers and the value spills out ( In a case like 255 + 1).

Example:
```
	mov rax, 0
    cmp r8, r9
    je .done 
    mov rax, 1 ; If r8 != to r9 mov 1 into rax
    jb .done ; If r8 is less than r9 jump to done with the value of 1 in rax
    mov rax, 2 ; If r8 !< r9 mov 2 into rax 
    .done
    ret
```

Chapter Ending: 
A signed temperature arrives in r8, between -50 and 100. Return 0 below zero, 1 from zero through 30, and 2 above 30.
```
    mov rax, 0
    cmp r8, 0
    jl .done    ; If r8 = 0 jump to done value 0 stored in rax
    mov rax, 2  ; Only happens if jl is not true
    cmp r8, 30
    jg .done    ; If r8 > 30 jump to done value 2 stored in rax
    mov rax, 1  ; If jl and jg are both not true value 1 is stored in rax
    .done
    ret
```
---
## Counted Loops

A loop repeats a block by jumping backward to a label. 

A counted loop requires:
* Initialization
* Stopping Test
* Counter Update

```
	mov rax, 0
	mov rcx, 3
	.loop:
	cmp rcx, 0
	je .done
	add rax, 2
	dec rcx
	jmp .loop
	.done:
```

This block will compare rcx to 0, if they are not equal it will add 2 to the rax and decrease rcx by 1. This will continue until the rcx is 0.

---
## Conditional Loops

Some loops stop when a value reaches a goal rather than after a precomputed count. 

```
	mov rax, 0
	mov rbx, 2
	.loop:
	cmp rbx, 11
	jge .done
	add rbx, 3
	inc rax
	jmp .loop
	.done:
```

This block compares rbx to the value of 11, if rbx is less than 11 it will add 3 to the rbx and increase the rax by one thenrun through the loop again.

---
## Running Totals

A running total is an accumulator separate from the loop counter.

```
	mov rax, 0
	mov rcx, 1
	.loop:
	cmp rcx, 4
	jg .done
	add rax, rcx
	inc rcx
	jmp .loop
	.done:
```

---
## Skipping and Stopping

A branch can skip selected work without ending the loop.

```
	mov rax, 0
	mov rcx, 1
	.loop:
	cmp rcx, 4
	jg .done
	cmp rcx, 2
	je .advance
	add rax, rcx
	.advance
	inc rcx
	jmp .loop
	.done:
```

This block does the following:
* Compare rcx to the value of 4, if greater than 4 jump to done.
* If less than 4, compare rcx to 2
* If rcx is equal to 2 jump to .advance
* If rcx is not equal to 2 add rax to rcx
* .advance will increase the rcx by one and jump through the loop again.

---
## AND Masks

Bitwise `and` works independently on each bit and compares two binary numbers and keeps a one when both numbers have a one in that exact same spot.

Ex:
```
mov rax, 29
and rax, 15
```

| 29 | 15 | Kept |
|----|----|------|
| 0 | 0 | 0 |
| 0 | 0 | 0 |
| 0 | 0 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |
| 1 | 1 | 1 |
| 0 | 1 | 0 |
| 1 | 1 | 1 |

This will result in the value of 13 being saved in the RAX

```
    mov rax, r8
    and rax, 1  ; Compares all the values to 1 to determine even (0) and odd (1) numbers
    mov rbx, r9
    and rbx, 1  ; Compares all the values to 1 to determine even (0) and odd (1) numbers
    add rax, rbx    ; Determines how many values are odd
    ret
```
 
 ---
 ## OR Masks

 `OR` compares two values and only sets a 1 bit if either input has a 1, if both are 0 then it will set the bit to 0.

| 29 | 15 | Kept |
|----|----|------|
| 0 | 0 | 0 |
| 0 | 0 | 0 |
| 0 | 0 | 0 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |
| 1 | 1 | 1 |
| 0 | 1 | 1 |
| 1 | 1 | 1 |

This is equivalent to 31 in decimal and will be saved in RAX.

---
## XOR Masks

`XOR` compares two values and outputs 1 only when the two input bits are different.

| 29 | 15 | Kept |
|----|----|------|
| 0 | 0 | 0 |
| 0 | 0 | 0 |
| 0 | 0 | 0 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |
| 1 | 1 | 0 |
| 0 | 1 | 1 |
| 1 | 1 | 0 |

This is equivalent to 18 in decimal and will be saved in RAX.

---
## Logical Shifts

`SHL` shifts each bit a certain number of bit spaces left.

```
mov rax, 5
shl rax, 2

5 is equivalent to 0000 0101
Shift Left 2 will move it to    0001 0100
```

`SHR` shifts each bit a certain number of bit spaces right.

```
mov rax, 10
shr rax, 1

10 is equivalent to 0000 1010
Shift Right 1 will move it to    0000 0101
```

Challenge:
```
    mov rax, r8
    or rax, 4   ; Enables bit 2 which has a decimal value of 4
    xor rax, 1  ; Toggles bit 0 which has a decimal value of 1
    and rax, 15 ; Keeps only the lower four bits since 15 = 0000 1111
    ret
```

---
## Array Offset

An array offser is the number if bytes you need to jump forward from the start of an array to reach a specific item.

If four input numbers are stored in an qword array of values we can select which to load into rax byt specifying the byte value associated with the value.

* 0 = first element
* 8 = second element
* 16 = third element
* 24 = fourth element

```
    mov rax, [values + 16]
    ret
```

In the following scenario, we will be subtracting the first element from the last element:
```
    mov rcx, [values + 0]
    mov rax, [values + 24]
    sub rax, rcx
    ret
```

---
## Index Addressing


Indexed addressing is a way in assembly language to find a memory location by combining a starting address with an index number.

In the example below we give the rcx a value, we use that value to specify which element we want to use. We also use (* 8) to specify that each item in values is 8 bytes wide.

```
mov rcx, 2 ; 0 = first element, 1 = second element, 2 = third element, 3 = fourth element
mov rax, [values + rcx * 8]
```

---
## Traversing an Array

Traversing an array in assembly means using a loop and memory addressing to read or modify every element one by one.

For example, below is a block that will iterate through every byte of data in a register and determine the xor value of it:
```
xor loop:
    xor rdx, 0
.next_byte:
    cmp rdx, rcx    ; Compares rdx value to rcx value (Length of the payload)
    jge .done   ; Once rdx value is equivalent to rcx (All bytes in rcx have been looped through) it will jump to done
    xor byte [rsi + rdx], bl    ; Grabs the single byte in memory starting with 0 or the first character in the payload
    inc rdx     ; Increases rdx so that on next loop iteration the next character is grabbed.
    jmp .next_byte
.done:
ret 
```

---
## Finding Extremes

Extremes refer to finding the maximum and minimum signed value that a register can hold.

```
    mov rax, [values]
    mov rcx, 1
.loop:
    cmp rcx, 4
    jae .done
    mov rbx, [values + rcx * 8]
    cmp rbx, rax
    jle .next
    mov rax, rbx
.next:
    inc rcx
    jmp .loop
.done:
```

```
    mov rax, [values]
    mov rcx, 1
.loop:
    cmp rcx, 4
    jae .finish ; This will close out the loop once the loop counter reaches 4
    mov rbx, [values + rcx * 8] ; Fetches the next 64 bit integer and stores in RBX
    cmp rbx, rax    ; Compares the newly set rbx to the current rax minimum
    jge .next   ; Jump if Greater than or Equal. if the RBX is greater than the current RAX value it jumps to .next
    mov rax, rbx ; If RBX is less than RAX we move that value into RBX
.next:
    inc rcx ; If RBX was greater than the RAX we'll increase the rcx by 1 increasing our loop counter
    jmp .loop   ; Jumping back into the loop
.finish:
    cmp rax, 0  ; When the loop counter reaches four we'll compare the RAX to 0
    jl .below   ; If the RAX is less than 0 Jump to the .below
    mov rax, 0  ; If the RAX is greater than 0 mov 0 into the RAX and jump to .done
    jmp .done
.below:
    mov rbx, 0 ; If the RAX was 0, we'd jump here and mov 0 into the RBX
    sub rbx, rax    ; Next we would subtract RBX from the RAX and store that value in rbx
    mov rax, rbx    ; Lastly we would mov the RBX value into RAX to output
.done:
ret
```

**STOCK REORDER CHALLENGE:**

Task: Four nonnegative stock counts arrive in array values, each at most 100. A shelf needs a reorder when its stock is below 5. Return the number of shelves needing a reorder.
Values[4, 5, 0, 9]

```
    mov rax, 0
    mov rcx, 0
.loop:
    cmp rcx, 4
    jae .done
    mov rbx, [values + rcx * 8]
    cmp rbx, 5
    jl .next
    inc rcx
    jmp .loop
.next:
    inc rax
    inc rcx
    jmp .loop
.done:
    ret
```

**Odd Value Total**

Task: Sum the odd values in the four-input array values.
```
mov rax, 0
    mov rcx, 0
.loop:
    cmp rcx, 4
    jae .done
    mov rbx, [values + rcx * 8]
    test rbx, 1 ; Checks the last bit of the value binary. Even numbers last bit is 0 and odd numbers last bit is 1
    jz .skip    ; Jump if Zero (ZF = 0) Even numbers will set ZF = 1, odd numbers will set ZF = 0
    add rax, rbx
    inc rcx
    jmp .loop
.skip:
    inc rcx
    jmp .loop
.done
    ret
```

**Range of Reading**

Task: Four signed readings arrive in array values, each between -100 and 100. Return the largest reading minus the smallest reading.
```
    mov rax, [values]           ; Initialize rax with first element (tracks smallest)
    mov rdx, [values]           ; Initialize rdx with first element (tracks largest)
    mov rcx, 1                  ; Start loop index at 1

.loop:
    cmp rcx, 4                  ; Check if we reached the end of the 4-element array
    jae .done                   ; If rcx >= 4, exit the loop

    mov rbx, [values + rcx * 8] ; Load current element into rbx

    ; --- Check for Smallest ---
    cmp rbx, rax                ; Compare current element (rbx) with current min (rax)
    jge .check_max              ; If rbx >= rax, it's not smaller; skip updating min
    mov rax, rbx                ; Otherwise, update rax with the new smallest value

.check_max:
    ; --- Check for Largest ---
    cmp rbx, rdx                ; Compare current element (rbx) with current max (rdx)
    jle .next                   ; If rbx <= rdx, it's not larger; skip updating max
    mov rdx, rbx                ; Otherwise, update rdx with the new largest value

.next:
    inc rcx                     ; Move to the next array index
    jmp .loop                   ; Repeat loop

.done:
    sub rax, rdx                ; Compute (smallest - largest) and store result in rax
    ret                         ; Return with final result in rax

```
