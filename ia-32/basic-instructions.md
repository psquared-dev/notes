
## Table of Contents
- [Data Units](#data-units)
- [General Purpose Registers \(x86-64 bit views\)](#general-purpose-registers-x86-64-bit-views)
- [Function Arguments \(Windows x64 Calling Convention\)](#function-arguments-windows-x64-calling-convention)
- [Function Return Value](#function-return-value)
- [Register Purpose](#register-purpose)
- [Where can I move data from and to?](#where-can-i-move-data-from-and-to)
- [MOV Instruction](#mov-instruction)
- [ADD Instruction](#add-instruction)
- [Sub Instruction](#sub-instruction)
- [MUL Instruction](#mul-instruction)
- [DIV Instruction](#div-instruction)
- [IMUL Instruction - Multiplying Signed Numbers](#imul-instruction---multiplying-signed-numbers)
- [IDIV Instruction - Dividing Signed Numbers](#idiv-instruction---dividing-signed-numbers)
- [Shifting Bits](#shifting-bits)
- [Rotating Bits](#rotating-bits)
- [Flags](#flags)
- [Testing Bit Values](#testing-bit-values)
- [Comparing Values](#comparing-values)
- [Addressing Options](#addressing-options)
- [push value](#push-value)
- [pop register](#pop-register)

## Data Units

*	BYTE – Byte (8-bits) range 0-255, or -128 to 127
* 	WORD – Word (16-bits) range 0-65,535, or -32,768 to 32,767
*	DWORD – Double word (32-bits) range 0-2<sup>32</sup>, or -2<sup>31</sup> to 2<sup>31</sup>-1 
*	QWORD – Quad word (64-bits) range 0-2<sup>64</sup>, or -2<sup>63</sup> to 2<sup>63</sup>-1

## General Purpose Registers (x86-64 bit views)

| 64-bit | 32-bit | 16-bit | 8-bit High | 8-bit Low |
|--------|--------|--------|------------|-----------|
| RAX    | EAX    | AX     | AH         | AL        |
| RBX    | EBX    | BX     | BH         | BL        |
| RCX    | ECX    | CX     | CH         | CL        |
| RDX    | EDX    | DX     | DH         | DL        |
| RSI    | ESI    | SI     | —          | SIL       |
| RDI    | EDI    | DI     | —          | DIL       |
| RBP    | EBP    | BP     | —          | BPL       |
| RSP    | ESP    | SP     | —          | SPL       |
| R8     | R8D    | R8W    | —          | R8B       |
| R9     | R9D    | R9W    | —          | R9B       |
| R10    | R10D   | R10W   | —          | R10B      |
| R11    | R11D   | R11W   | —          | R11B      |
| R12    | R12D   | R12W   | —          | R12B      |
| R13    | R13D   | R13W   | —          | R13B      |
| R14    | R14D   | R14W   | —          | R14B      |
| R15    | R15D   | R15W   | —          | R15B      |

```text
64-bit: RAX
┌───────────────────────────────────────────────────────────────┐
│                          RAX (64 bits)                        │
└───────────────────────────────────────────────────────────────┘
                             │
                             ▼
32-bit: EAX
┌────────────────────────────┐
│           EAX              │  (lower 32 bits of RAX)
└────────────────────────────┘
               │
               ▼
16-bit: AX
┌──────────────┐
│      AX      │  (lower 16 bits of EAX)
└──────────────┘
       │
       ▼
  8-bit halves of AX
┌───────┬───────┐
│  AH   │  AL   │
└───────┴───────┘
```

## Function Arguments (Windows x64 Calling Convention)

When a function is called:

1. 1st argument → RCX
1. 2nd argument → RDX
1. 3rd argument → R8
1. 4th argument → R9

Any arguments beyond the 4th are passed on the stack.

## Function Return Value

* RAX → holds the function return value. If it’s up to 64 bits, e.g., int, pointer, double.
* For bigger return types, memory is used.

## Register Purpose

| Register | Historical name   | Typical role                               | Calling convention (Windows x64) | Volatility   |
|----------|-------------------|--------------------------------------------|----------------------------------|--------------|
| RAX      | Accumulator       | Arithmetic, multiply/divide, return values | Function **return value**        | Volatile     |
| RBX      | Base              | Loop/index, general storage                | Callee-saved                     | Non-volatile |
| RCX      | Counter           | Used in loops (`LOOP`), string ops         | **1st function argument**        | Volatile     |
| RDX      | Data              | Used in I/O ops, multiply/divide remainder | **2nd function argument**        | Volatile     |
| RSI      | Source Index      | String source pointer                      | Callee-saved                     | Non-volatile |
| RDI      | Destination Index | String destination pointer                 | Callee-saved                     | Non-volatile |
| RBP      | Base Pointer      | Frame pointer (stack frame)                | Callee-saved                     | Non-volatile |
| RSP      | Stack Pointer     | Points to top of stack                     | Special (stack mgmt)             | Non-volatile |
| R8       | —                 | Extra argument                             | **3rd function argument**        | Volatile     |
| R9       | —                 | Extra argument                             | **4th function argument**        | Volatile     |
| R10      | —                 | Scratch / syscall #                        | Temp                             | Volatile     |
| R11      | —                 | Scratch / flags save                       | Temp                             | Volatile     |
| R12–R15  | —                 | Extra general storage                      | Callee-saved                     | Non-volatile |

## Where can I move data from and to?

* CPU registers can both send and receive data
* Memory can send or receive, but not both at the same time (no mem → mem directly)
* Immediate values (constants) can only be sent, never received

These rules apply to most instructions.


## MOV Instruction

```text
MOV     Destination , Source
```
* Destination – a register name, or a memory variable.
* Source – a register name, a memory variable, or an "immediate" operand – typically a numeric value.

It is important to recognize that both operands must be of the same size. Assigning a variable to a 64-bit register 
therefore requires the variable to be 64 bits in size - a quad word.

Example of invalid instruction:

```text
mov [0x0000000712], 10
```

* In Real Mode, this would directly write the byte `0x0A` into physical memory address `0x0712`.
* In Protected Mode:
    * Now that address (`0x0000000712`) is a virtual address.
    * On Windows/Linux user-space, low memory (below `0x10000` typically) is reserved and not mapped.
    * So when your instruction executes, the CPU will raise a segmentation fault / access violation.

**Note:** In Read Mode, there is no concept of paging or virtual memory, all addresses are treated as physical addresses.

### Variable Declaration

Variables must be declared in the data section of the Assembly code by specifying a name of your choice, the data 
allocation size, and an initial value, with this syntax:

```text
Variable-name     Data-allocation     Initial-value
```

The variable name can begin with any letter `A-Z` (in uppercase or lowercase) or any of the characters `@_$?`. 
The remainder of the name can contain any of those characters and numbers `0-9`. Variable names throughout this book 
are lowercase, to easily distinguish them from the register names in all uppercase.

### Variable Initialization

An initial value is typically specified in the variable declaration, but can be replaced by a `?` question mark character 
if the variable is to be initialized later in the program.

## ADD Instruction

```text
ADD  Destination, Source
```
Rule 1: Destination can be:

* A register (RAX, RCX, etc.)
* A memory location (variable, pointer, etc.)

Rule 2: Source can be:

* A register
* A memory location
* An immediate constant

Rule 3: You can’t have both operands be memory

* Because CPU can’t directly do mem + mem. At least one operand must be a register.

## Sub Instruction

```text
SUB Destination, Source
```
Same rules as `ADD` instruction.

## MUL Instruction

The Assembly `MUL` instruction can be used to multiply an unsigned value in a register or a memory variable. 
The `MUL` instruction requires just one operand and has this syntax:

```text
MUL Multiplier
```

Multiplier – a register name or a memory variable containing the number by which to multiply a multiplicand.

The multiplicand (number to be multiplied) should be placed in a specific register matching the multiplier’s size. 
The multiplication process places the upper half and lower half of the result in two specific registers – the result 
is twice the size of the multiplicand.

| Operand size | Multiplicand (implicit) | Result  | Result – Upper Half | Result – Lower Half |
|--------------|-------------------------|---------|---------------------|---------------------|
| 8-bit        | AL                      | AX      | AH                  | AL                  |
| 16-bit       | AX                      | DX:AX   | DX                  | AX                  |
| 32-bit       | EAX                     | EDX:EAX | EDX                 | EAX                 |
| 64-bit       | RAX                     | RDX:RAX | RDX                 | RAX                 |


From the above table, it can be concluded that if multiplier is 32 bits then it would be multiplied by `RAX` and 
the upper-half of the result would be in `RDX` and lower-half would be in `RAX`.

Invalid instruction example:

```text
MUL 4
```

Most assemblers (MASM/NASM) will error out, because `MUL` only supports reg or mem, not immediates.

## DIV Instruction

```text
DIV Divisor
```

| Operand size | Dividend (double size) | Quotient SAL | Remainder |
|--------------|------------------------|--------------|-----------|
| **8-bit**    | AX                     | AL           | AH        |
| **16-bit**   | DX:AX                  | AX           | DX        |
| **32-bit**   | EDX:EAX                | EAX          | EDX       |
| **64-bit**   | RDX:RAX                | RAX          | RDX       |

From the above table, we can conclude that if divisor is 32 bits then the values in `EDX` and `EAX` will be 
concatenated and quotient will be placed in `EAX` and remainder in `EDX`.

## IMUL Instruction - Multiplying Signed Numbers

```text
IMUL    Multiplier
```

Multiplier: a register name, a memory variable, or an immediate value specifying the number by which to multiply the multiplicand.

With one operand, the multiplicand (the number to be multiplied) should be placed in a specific register matching the multiplier’s size, following the same pattern as that for the `MUL` instruction described in the section on `MUL` Instruction.

The `IMUL` instruction can accept two operands, with this syntax:

```text
IMUL    Multiplicand, Multiplier
```

* Multiplicand: a register name containing the number to be multiplied.
* Multiplier: a register name, a memory variable, or an immediate value specifying the number by which to multiply the multiplicand.

The `IMUL` instruction can accept three operands, with this syntax:

```text
IMUL    Destination, Multiplicand, Multiplier
```

* Destination: a register name where the result will be placed.
* Multiplicand: a register name or memory variable containing the number to be multiplied.
* Multiplier: an immediate value specifying the number by which to multiply the multiplicand.

It is important to note that the multiplicand and multiplier (and destination in the three-operand format) must be the same bit size. Additionally, note that when using the two-operand format, the result may be truncated if it’s too large for the multiplicand register. If this occurs, the "overflow flag" and the "carry flag" will be set, so these can be checked if you receive an unexpected result.


## IDIV Instruction - Dividing Signed Numbers

```text
IDIV    Divisor
```

Divisor: a register name or a memory variable specifying the number by which to divide the dividend.

The dividend (the number to be divided) should be placed in a specific register or registers twice the size of the divisor. The division process places the resulting quotient and any remainder in two specific registers.

| Operand size | Dividend (double size) | Remainder | Quotient |
| ------------ | ---------------------- | --------- | -------- |
| **8-bit**    | AX                     | AH        | AL       |
| **16-bit**   | DX:AX                  | DX        | AX       |
| **32-bit**   | EDX:EAX                | EDX       | EAX      |
| **64-bit**   | RDX:RAX                | RDX       | RAX      |


| Instruction | Meaning               | Source → Destination (Sign-extended) |
| ----------- | --------------------- | ------------------------------------ |
| **CBW**     | Byte → Word           | AL → AH:AL                          |
| **CWD**     | Word → Doubleword     | AX → DX:AX                          |
| **CDQ**     | Doubleword → Quadword | EAX → EDX:EAX                       |
| **CQO**     | Quadword → Octoword   | RAX → RDX:RAX                       |


## Shifting Bits

Each shift left by one bit doubles the numerical value; each shift right by one bit halves the numerical value. 

[Arithmetic Shift](https://www.youtube.com/shorts/GjmiiLzDzHI)

### SHL – Shift Left (Logical)

* Operation: Shift all bits left; zeros fill the LSB.
* Effect: Multiply by 2 for each shift.

```text
Number: 0010 1101  (decimal 45)
SHL by 1 bit → 0101 1010  (decimal 90)
```

*  Bits move left: 0010 1101 → 0101 1010
* LSB = 0
* MSB (bit 7) is lost if it overflows


### SHR – Shift Right (Logical)

* Operation: Shift all bits right; zeros fill the MSB.
* Effect: Divide by 2 for each shift (ignores sign).

```text
Number: 0010 1100  (decimal 44)
SHR by 1 bit → 0001 0110  (decimal 22)
```

* Bits move right: 0010 1100 → 0001 0110
* MSB = 0
* LSB falls off

### SAL – Shift Arithmetic Left

* Operation: Same as SHL.
* Effect: Multiply by 2 for each shift; preserves arithmetic meaning.

```text
Number: 0001 1010  (decimal 26)
SAL by 1 bit → 0011 0100  (decimal 52)
```

**Note**: SAL = SHL. They are identical for left shift.


### SAR – Shift Arithmetic Right

* Operation: Shift bits right; MSB (sign bit) stays the same, others move right.
* Important: The MSB (sign bit) stays the same, and the vacant positions on the left are filled with the original MSB.
* Effect: Divides signed number by 2 (rounding toward negative).

```text
Number: 0010 1100  (decimal 44)
SAR by 1 bit → 0001 0110  (decimal 22)
```

**Example (negative number, 8-bit 2’s complement):**

```text
Number: 1110 1100  (-20)
SAR by 1 bit → 1111 0110  (-10)
```

* MSB = 1 → stays 1
* Vacant position filled with 1
* Other bits shift right
* LSB falls off

**Example (positive number):**

```text
Number: 0010 1100  (44)
SAR by 1 bit → 0001 0110  (22)
```

* MSB = 0 → stays 0
* Vacant position filled with 0


**Note:** In all the above operations the bit that moves past the edge does go into `CF`.


## Rotating Bits

### RCL – Rotate through Carry Left

Syntax:

```text
RCL destination, count
```

Behavior:
* The bits of destination are rotated left.
* The carry flag (CF) is included in the rotation.
* The MSB that “falls off” goes into CF, and the previous CF goes into LSB.

Example (8-bit):

```text
CF = 1
AL = 1011_0010
RCL AL, 1
```

Step:
* Shift AL left by 1 → 0110_010_?
* Old CF (1) goes into LSB → 0110_0101
* MSB (1) goes into CF → CF = 1
* Result: AL = 0110_0101, CF = 1.

## Flags

* Carry Flag (CF): CF = 1 → the unsigned result doesn’t fit in the type
* Overflow Flag (OV): OV = 1 → the signed result doesn’t fit in the type
* Sign Flag (PL): This gets set if the result of the previous instruction was a negative value.
* Zero Flag (ZR): This gets set if the previous result was zero.

### OV flag example:

```text
	XOR RAX, RAX
	mov AL, 127
	mov AH, 127
	add AL, AH
```

After the `add AL, AH` operation, `OV` flag is set to 1.

### CF flag example:

```text
	XOR RAX, RAX
	mov AL, 250
	mov AH, 250
	add AL, AH
```

After the `add AL, AH` operation, `CF` flag is set to 1.

## Testing Bit Values

This works the same as the `AND` instruction, but does not change the value in the first operand.

```text
TEST    Destination, Source
```

* Destination: a register or memory variable containing the binary value to be tested.
* Source: a register, memory variable, or immediate value containing a binary pattern for comparison.

When the `TEST` instruction returns a `0`, the `ZF` gets set to `1` otherwise the `ZF` gets set to `0`.

## Comparing Values

```text
CMP Left-Operand , Right-Operand
```

* `CMP` subtracts the right operand from the left operand (Left-Operand - Right-Operand).
* It does not keep the result, it only updates flags.
* Those flags describe what the result would have been, so you can do conditional jumps.

### Flags affected by CMP

* Zero Flag (`ZF`): set if operands are equal.
* Sign Flag (`SF`): set if the (subtraction) result is negative.
* Carry Flag (`CF`): set if there was a borrow (i.e., if left < right in unsigned terms).
* Overflow Flag (`OF`): set if signed overflow occurs.
* Parity Flag (`PF`): set if the low byte of the result has even parity.

| Mnemonic | Alias    | Meaning (English)            | Type     | Condition (Flags)  |
| -------- | -------- | ---------------------------- | -------- | ------------------ |
| **JE**   | JZ       | Jump if Equal                | Any      | ZF = 1             |
| **JNE**  | JNZ      | Jump if Not Equal            | Any      | ZF = 0             |
| **JA**   | JNBE     | Jump if Above (>)            | Unsigned | CF = 0 AND ZF = 0  |
| **JAE**  | JNB, JNC | Jump if Above or Equal (≥)   | Unsigned | CF = 0             |
| **JB**   | JNAE, JC | Jump if Below (<)            | Unsigned | CF = 1             |
| **JBE**  | JNA      | Jump if Below or Equal (≤)   | Unsigned | CF = 1 OR ZF = 1   |
| **JG**   | JNLE     | Jump if Greater (>)          | Signed   | ZF = 0 AND SF = OF |
| **JGE**  | JNL      | Jump if Greater or Equal (≥) | Signed   | SF = OF            |
| **JL**   | JNGE     | Jump if Less (<)             | Signed   | SF ≠ OF            |
| **JLE**  | JNG      | Jump if Less or Equal (≤)    | Signed   | ZF = 1 OR SF ≠ OF  |


## Addressing Options

### Types of addressing

### Register Addressing

* Definition: Operand is in a CPU register.

Example:

```text
MOV AX, BX   ; Copy value from register BX into register AX
ADD CX, DX   ; Add DX to CX, store result in CX
```

* Explanation: No memory access; CPU just uses the register values.


### Immediate Addressing

* Definition: Operand is a constant value coded directly in the instruction.


Example:

```text
MOV AX, 10   ; Load the value 10 into AX
ADD BX, 5    ; Add 5 to BX
```

* Explanation: The number itself is part of the instruction.


### Direct Memory Addressing

* Definition: Operand is stored at a specific memory address.

```text
MOV AX, [1234h] ; Load the value from memory address 0x1234 into AX
MOV [2000h], BX  ; Store BX into memory address 0x2000
```

* Explanation: Instruction points directly to the memory location.

### Direct Offset (or Based) Addressing

* Definition: Operand is at a memory address computed by adding an offset to a base address.

```text
MOV AX, [1000h + 10] ; Load value at memory address 0x1000 + 10 into AX
MOV [2000h + SI], BX  ; Store BX into memory address 0x2000 + contents of SI
```

* Explanation: You modify a fixed memory address by an offset (like array indexing).

### Indirect Memory Addressing

* Definition: Operand is at a memory location pointed to by a register.

```text
MOV AX, [BX]   ; Load value from memory location whose address is in BX
MOV [SI], AX   ; Store AX into memory location whose address is in SI
```

* Explanation: The register holds the address of the operand rather than the value itself.


### LEA Instruction

* LEA = Load Effective Address
* It loads the calculated memory address (pointer) into a register, not the value stored at that address.

```text
LEA destination, source
```
* `destination` ← address computed from `source`

## push value

* esp <- esp - 4
* dword[esp] <- value

## pop register

* register  <- dword[esp]
* esp <- esp + 4

