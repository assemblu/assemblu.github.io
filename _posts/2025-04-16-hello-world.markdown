---
layout: post
title:  "Hello World"
date:   2025-04-16 16:04:46 +0200
categories: reverse-engineering binary-exploitation kernel anti-cheat
---

# Hello World!

Welcome to my reverse engineering blog. I'll be sharing my adventures in binary exploitation and kernel anti-cheat bypass techniques.

As every programmer starts with a "Hello World", here's mine in assembly:

{% highlight asm %}
section .data
    msg db "Hello, World of Reverse Engineering!", 0x0A
    len equ $ - msg

section .text
    global _start

_start:
    mov eax, 4      ; sys_write
    mov ebx, 1      ; stdout
    mov ecx, msg    ; message
    mov edx, len    ; length
    int 0x80        ; call kernel
    
    mov eax, 1      ; sys_exit
    xor ebx, ebx    ; exit code 0
    int 0x80        ; call kernel
{% endhighlight %}

Stay tuned for future posts on:
- Buffer overflow techniques
- Kernel driver manipulation
- Anti-cheat bypass methods
- Binary analysis tools

All content on this blog is for educational purposes only.
The great restart ensues.