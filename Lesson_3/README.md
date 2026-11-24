## PUSH and POP

The stack is a region of memory your program uses to keep values for a short time.

The `rsp` holds the address of the value on top of the stack. When the program starts, the `rsp` immediately points to the stack.

`push` puts an 8-byte value on the `rsp`. For example:
```
; rsp = 1000

push rdi ; Subtracts 8-bytes from `rsp` then stores `rdi` at the new address in rsp

; `rdi` is stored at 992
```

`pop` copies 8-bytes at the address in `rsp` into the target register, then adds 8 8 to the `rsp`

```
; rsp = 984

pop rax ; rax = rsp, rsp = 992
```
`PUSH` and `POP` can swap two registers without a third one.

```
    mov rax, 0
    push rdi ; `rdi` is pushed to the stack
    push rsi ; `rsi` is pushed to the top of the stack
    pop rdi ; The top value in the stack `rsi` is copied into `rdi` 8 bytes are added to shrink the stack
    pop rsi ;  The top value which is not the previous value of `rdi` is copied into `rsi`
```



```
