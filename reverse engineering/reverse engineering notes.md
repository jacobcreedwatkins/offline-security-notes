##### CCTC Challenge Server Resources 
- Reverse Engineering Workflow
- x86 student guide
- memory diagram
- stack practice
- the stack

##### Programming

c+ Code -> Compiler -> Program.exe

Program.exe -> Decompiler -> Low Level Language (assembly)

# Assembly and Registers
- Assembly Instruction format: **Instruction Operand1, Operand2**
```
Instruction Operand1
Insruction Operand1, Operand2


Operand:
Hex Value | 0x3
Register | eax
Memory Address | 0x00000001

Instruction

### Data Movement/Storage
MOV
LEA

### ARITHMETIC
ADD
SUB
INC
DEC
MUL
DIV

### LOGIC
OR
AND
XOR
SHR
SHL

### STACK
PUSH
POP
CALL
RET

### CONTROL FLOW
TEST
CMP	((compare))
JMP	(unconditional jump)
JLE/JE	(conditional jump)
```

##### GySgt's Example
```
((read these top to bottom))

#data storage/movement
mov <dst>,<src>
mov eax,100	| eax=100
mov ebx,200	| eax=100,ebx=200
mov eax,200	| eax=200,ebx=200
mov eax,300	| eax=300,ebx=200
mov eax,ebx	| eax=200,ebx=200
-----------------------------------

#arithmetic
add <dst>,<src>
add eax,30	| eax=230,ebx=200
sub ebx,60	| eax=230,ebx=140
inc eax		| eax=231,ebx=140
dec ebx		| eax=231,ebx=139
-----------------------------------


sub vs cmp:	compare statement performs a comparison on operands, the result of which affects what status is set by the flags register
		subtract literally subtracts


#compare operand
cmp operand1 - operand2
cmp eax,231
cmp eax - 231		| eax=231,ebx=139
cmp 231 - 231 = 1	| flags register : zero flag : 

#flags register (also specific to this problem)
zero flag (set if 0)			= 0
sign flag (set if negative) 		= 0
carry flag (set if too small/large) 	= 1


#example jump instruction
jle 0x00000001
eax = 10
cmp eax,10
cmp 10-10 = 0

	#therefore flags register:
	zero flag (0)	:	1
	carry flag (int):	0
	sign flag (neg)	:	0
	
je 0x00000001
- the jump instruction relies on the status of the flags register
	#je	:	jump if equal
	jle	:	jump oif less than or equal
	jg	:	jump if greater than
	jge	:	jump if greater than or equal to


#stack movement ex1
last in, first out; the stack

main:
	mov rax, 16	| rax=16
	push rax	| rax=16 | stack=16
	jmp mem2	| 
mem1:
	mov rax, 0	| rax=0,r8=16
	ret (returns the value of the first return register, rax) 0
mem2:
	pop r8		| rax=16,r8=16 | stack=16	#pop value on the stack (16) into r8
	cmp rax,r8					#cinoare rax register value (16) to r8 register value
		16 - 16 = 0
		Flag Register : Zero Flag : 1
	je mem1		| rax=16,r8=16
	
------------------------------------------------------------------------------------------------------------------------------------------------------


#stack movement ex2
main:
    mov rcx, 25     //store the value 25 in rcx register	#rcx=25
    mov rbx, 62     //store the value 62 in rbx register	#rbx=62
    jmp mem1        //jumps to mem1 location			

mem1:
    sub rbx, 40     //subtract 40 from rbx			#rbx=22
    mov rsi, rbx    //copy rbx value to rsi			#rsi=22,r bx=22
    cmp rcx, rsi    //compare the values in rcx and rsi		#rcx=25,rsi=22	->	25-22=3
    jmple mem2      //jumps to mem2 location if value is less than or equal. in this case it shouldnt since the carry flag is set (jle compares it to 0); however the code also executes sequentially so mem2 would be executed anyways
    

mem2:
    mov rax, 0      //store 0 in rax
    ret             //return out of code
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```

# Examining C source code

	`main():`
	> indicates main entry point and start of program

	`printf`
	> sends formatted output to stdout

	- %d = signed decimal integer
	- %f = decimal floating point
	- %s = strings

	`fgets():`
	> reads a line from the specified stream and stores it inot the string pointned ot by str.
	`char *fgets(char *str, in n, FILE *stream)`

	`strcmp():`
	> compares str1 and str2


	`atoi():`
	> ascii to integer

	`strtok():`
	stuff "This is an example!"
	```
	strtok(stuff):
	This
	is
	an
	example
	```



Download:
- Windows and Linux Ops Station
- Download to one and then scp to the other, or create a python3 HTTP server, or manually download both on each workstation
##### download the following:

1. Demo1.c
2. Demo1.exe
3. Demo2.c
4. Demo2.exe






## x86_64 Assembly Common terms

	- Heap:			Memory that can be allocated and deallocated
	- Stack:		A contiguous section of memory used for passing arguments
	- General Register	A multipurpose register that can be used by either programmer or user to store data or a memory location address
	- Control Register	A processor register that changes or controls the behavior of a CPU
	- Flags Register	Contains the current status of the processor


#Registers
	 - **Registers can store values**
	 - There are 16 general purpose 64 bit registers
	 - 64 bit registers begin with `r`
	 - 32 bit registers begin with `e
	 `
	- %rax	:	the first return register
	- %rbp	:	the base pointer that keeps track of the base (bottom) of the stack; static
	- %rsp	:	the stack pointer that points to the top of the stack; dynamic
	- %rip	:	instruction pointer; points to the memory offset of the next instruction
 
## x86_64 Assembly Common Instruction Pointers
	((update later))
	
	
# Binary Analsysis

## Steps:

1. Static Analysis
2. Behavior Analysis
3. Dynamic Analysis
	- Sysinternals tools: procmon, procexp
4. Disassembler and Decompiler
5. Documentation


##### Demo1.c

	```cpp

	  1 #include <windows.h>
	  2 #include <stdio.h>
	  3 #include <string.h>
	  4 #include <stdlib.h>
	  5 
	  6 int firstKey(key1)
	  7 {
	  8     int key2 = atoi(key1);
	  9     int p2 = 29;
	 10     if ((key2-123)==0)
	 11     {
	 12         return 13555;
	 13     }
	 14     return 12;
	 15 }
	 16 
	 17 int main(void)
	 18 {
	 19     char key1[20];
	 20     printf("Enter Key: ");
	 21     fgets(key1,20,stdin);
	 22     strtok(key1, "\n");
	 23     if (firstKey(key1)==13555)
	 24     {
	 25         printf("123 is 123.\n");
	 26         Sleep(5000);
	 27         return 0;
	 28     }
	 29     else
	 30     {
	 31         printf("%s is not 123.\n", key1);
	 32         Sleep(5000);
	 33         return 0;
	 34     }
	 35 }
	 36 
	 37 

	```

### Step 1. Static Analysis (demo1.exe)

	`file <filename>`	:	determine type of file

[file signatures reference](https://en.wikipedia.org/wiki/List_of_file_signatures)

	`strings -n <number> <filename>`	:	print strings matching a given number of characters in length from a given file

	- Look for artifacts! interesting tidbits of code/things that match the C code

	- `gdb <filename>`
	- gdb = GNU debugger

##### GDB-Peda Commands

```bash
gdb-peda$ >> pdisass main





```
	

### Step 2. Behavior Analsyis

	- Run the file, play with it a bit and see what it does

### Step 4. Disassembler and Decmpiler

	- Ghidra
	- IDA

	- `file > new project > nonshared project > 
	- `file > import file > file_to_import`
	- compare hashes from ghidra summary and linux md5sum to verify file integrity


	```
	Example output from middle pane w/ their meaning:

	00407cc2   83 c0 08   ADD		EAX,0x8
	Memory	   Opcodes    ASM Instruction    Operands
	```

##### ghidra
`;`	semicolon is shortcut for command to add comments
`param_1`	means user input in ghidra


- search strings for artifacts to find functions and variables
- double click `FUN_` to open functions in ghidra
- to patch, right click and choose "patch instruction"
- after patching, export file as binary to a different name so that it does not overwrite the original file
- to export, ``file > export program > change to binary > give name of patched_<file.exe> so you know which one is patched`
- to run the patched file, .\<patched_file.exe>


=======================================================================================================================================================================

# GUNNY NGUYEN's NOTES


```
	Reverse Engineering WOrkflow
	x86 Student Guide
	Memory Diagram
	Stack Practice
	The Stack

	Programs
	C+
	High Level Language

	C+ Code > Compile > Program.exe
	Program.exe > Decompiler > Low Level Language (Assembly)

	Instruction Operand1,Operand2

	Register
	16 General Registers
	Stack Pointer
	Base Pointer
	Registers can store values 

	Hex Value
	Memory Address


	Flags Register:
	https://en.wikipedia.org/wiki/FLAGS_register


	Assembly Syntax
	Instruction Operand1
	Instruction Operand1, Operand2

	Operand:
	Hex Value | 0x3
	Register | eax
	Memory Address | 0x00000001

	Instruction:

	Data Movement/Storage
	MOV
	LEA

	Arithmetic
	ADD
	SUB
	INC
	DEC
	MUL
	DIV

	Logic
	OR
	AND
	XOR
	SHR
	SHL

	Stack
	PUSH
	POP
	CALL
	RET

	Control Flow
	TEST
	CMP
	JMP	#Unconditional Jump
	JLE #Conditional Jump, Jump if less than or equal to


	Instruction Operand1
	mov <dst>,<src>
	mov eax,100		| eax=100
	mov ebx,200		| eax=100,ebx=200
	mov eax,200		| eax=200,ebx=200
	mov eax,300		| eax=300,ebx=200
	mov eax,ebx		| eax=200,ebx=200

	add <dst>,<src>
	add eax,30		| eax=230,ebx=200
	sub ebx,60		| eax=230,ebx=140
	inc eax			| eax=231,ebx=140
	dec ebx			| eax=231,ebx=139


	sub vs cmp



	cmp operand1 - operand2
	cmp eax,231	
	cmp eax - 231	| eax=231,ebx=139
	cmp 231-231=0	| flags register : zero flag : 

	flags register
	zero flag (0)= 0
	sign flag (negative) = 0
	carry flag (too small or large) = 1

	jle	0x00000001


	eax = 11
	cmp eax,10
	cmp 11-10 = 1

	Flags Register
	Zero Flag (0): 0
	Carry Flag (int): 1
	Sign Flag (neg): 0

	je 0x00000001

	je = Jump if equal

	jl = Jump if less
	jle = Jump if less than or equal to
	jg = Jump if greater 
	jge = Jump if greater than or equal to
	je = Jump if equal


	main:
	  mov rax, 16 	|rax=16
	  push rax 		|rax=16 | stack=16
	  jmp mem2 
	mem1: 
	  mov rax, 0 	|rax=0,r8=16
	  ret (returns the value of the first return register, rax) 0
	mem2: 
	  pop r8 		|rax=16,r8=16
	  cmp rax, r8
			16-16=0 
			Flag Register:Zero Flag:1
	  je mem1		|rax=16,r8=16


	main:
	  mov rcx, 25 	|rcx=25
	  mov rbx, 62 	|rcx=25,rbx=62
	  jmp mem1 		|Unconditional Jump
	mem1: 
	  sub rbx, 40 	|rcx=25,rbx=22
	  mov rsi, rbx 	|rcx=25,rbx=22,rsi=22
	  cmp rcx, rsi 	|rcx=25,rbx=22,rsi=22

	  rcx - rsi
	  25 - 22 = 3
	  Flags Register:
		Zero Flag: 0
		Carry Flag: 1
		Sign Flag: 0

	  jle mem2 

	mem2: 
	  mov rax, 0 
	  ret 



	main():
	entry point and start of the program.

	printf():
	sends formatted output to stdout.

	%d = Signed decimal integer
	%f = Decimal floating point
	%s = Strings

	fgets():
	reads a line from the specified stream and stores it into the string pointed to by str.

	char *fgets(char *str, int n, FILE *stream)

	strcmp():
	compares str1 and str2

	fp = fileopen("file.txt","r")
	fgets(str,50,fp)

	fgets(num1,5,stdin);


	atoi():
	ascii to interger


	strtok():
	stuff = "This is an example!"

	strtok(stuff)
	This
	is
	an
	example

	scanf()
	Reads from stdin

	Download:
	-Windows and Linux Ops Station
	-SCP, Python3 HTTP Server, Download manually on both workstations

	Demo1.c
	Demo1.exe
	Demo2.c
	Demo2.exe
```
