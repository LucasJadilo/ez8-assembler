# EZ8 Assembler

The EZ8 Assembler was completely developed in C, somewhat inspired by the
Microsoft Macro Assembler (MASM).

This software converts an assembly program into the binary data that will be
executed by the EZ8 CPU, as illustrated in the diagram below.

```
┌──────𝅊
│ .asm │ Source assembly program
└──────┘
   │    ┏━━━━━━━━━━━━━━━┓
   ╰──> ┃ EZ8 Assembler ┃
        ┗━━━━━━━━━━━━━━━┛
            │    ┌──────𝅊
            ├──> │  .i  │ Preprocessed assembly program
            │    └──────┘
            │    ┌──────𝅊
            ├──> │ .hex │ Assembled program in hexadecimal format
            │    └──────┘
            │    ┌──────𝅊
            ╰──> │ .bin │ Assembled program in binary format (256-byte file)
                 └──────┘
```

The EZ8 is a fully simulated 8-bit RISC CPU designed from scratch as a hobby project.
Check the complete CPU documentation on [Hackster](https://www.hackster.io/LucasJadilo/ez8-8-bit-cpu-from-scratch-6d22d8).

## Build

All the project features, including the build process, code formatting, code analysis,
etc., are automated via a [Makefile](Makefile) (GNU Make required). Use the command
below to see the available options:

```sh
make help
```

This software depends on the [EZ8 C/C++ Library](https://github.com/LucasJadilo/ez8-lib-c).

## Assembly

In addition to the EZ8 instructions, the assembler also supports the following directives:

- `.code`: Indicates the start of a code segment, which may contain labels and
  assembly instructions. Every program must begin with a code section
- `.data`: Indicates the start of a data segment, which may contain labels and initialized data.
- `end`: Marks the end of a program.

Every line inside a code section must be written according to this syntax:

`[LABEL:] [MNEMONIC [OPERAND]] [;COMMENT]`

- `LABEL`: A string that serves as an alias for the address of the immediate next
  instruction or data. A label may contain letters (`a-z`, `A-Z`), numeric digits
  (`0-9`), and underscores (`_`). A label must start with a letter or underscore.
- `MNEMONIC`: An EZ8 instruction mnemonic.
- `OPERAND`: An EZ8 Instruction operand (only if required). It may be a label or
  an unsigned 8-bit integer. A prefix `0` indicates octal base, a prefix `0x` or
  `0X` indicates hexadecimal base, and no prefix indicates decimal base.
- `COMMENT`: single-line comments are inserted in the program using a semicolon (`;`).
  Everything after the semicolon, on the same line, is ignored.

Examples of valid code section lines:

```
.code
read_button: in 0xF8 ; Read the input port
next:
; line with comment
lda 15
nothing: nop
```

Every line inside a data section must be written according to this syntax:

`[LABEL:] [DATA-LIST] [;COMMENT]`

- `LABEL`: Same as the code section.
- `DATA-LIST`: List of unsigned 8-bit integers and/or double-quoted strings.
  Integers may have a prefix `0` to indicate octal base, a prefix `0x` or `0X` to indicate
  hexadecimal base, or no prefix to indicate decimal base. For double-quoted strings,
  every ASCII character inside the quotes will be allocated as an 8-bit integer.
- `COMMENT`: Same as the code section.

Examples of valid data section lines:

```
.data
data_01: 0x25 ; Useless data
0x10 0x20 0x30
; line with comment
1 "asdf" 2 0x5A
66
_data_str_: "blablabla"
```

Everything written in the program is case-insensitive, except labels and double-quoted strings.

Example of a syntactically valid assembly program:

```
.code

     LDA 0x10

  ; comment    comment
;

;;

Label_001: LDI 15   
NOP;comment
	NOP
  nOp
aAbBcC:
L003_:

nOP 	  
; comment   
   _lABel002_:   ADD  	30  ; comment
sub Label_001
    .daTa 
data01: 0x01    15


"asdf"
asdf: 10 "qwer"  0x23  
 
.Code
jmp Label_001
Xor   0x30  

jpA data01;             aaabbbccc
END

   
```

Preprocessed assembly program:

```
.code
LDA 0x10
Label_001: LDI 15
NOP
NOP
nOp
aAbBcC:
L003_:
nOP
_lABel002_: ADD 30
sub Label_001
.daTa
data01: 0x01 15
"asdf"
asdf: 10 "qwer" 0x23
.Code
jmp Label_001
Xor 0x30
jpA data01
END
```

Assembled program in hexadecimal format:

```
     00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F
     -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
00 : 00 10 01 0F 18 18 18 18 0A 1E 0B 02 01 0F 61 73  ..............as
10 : 64 66 0A 71 77 65 72 23 0D 02 07 30 0F 0C 00 00  df.qwer#...0....
20 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
30 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
40 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
50 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
60 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
70 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
80 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
90 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
A0 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
B0 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
C0 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
D0 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
E0 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
F0 : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
```
