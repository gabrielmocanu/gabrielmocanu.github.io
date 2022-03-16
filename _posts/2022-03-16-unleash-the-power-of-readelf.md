---
layout: post
---

# Readelf

## Intro

I think the name of the command is intuitive enough to tell us what it does, but ..

```
~ > readelf ./exec_file
Usage: readelf <option(s)> elf-file(s)
 Display information about the contents of ELF format files
 Options are:
  -a --all               Equivalent to: -h -l -S -s -r -d -V -A -I
  -h --file-header       Display the ELF file header
  -l --program-headers   Display the program headers
     --segments          An alias for --program-headers
  -S --section-headers   Display the sections' header
     --sections          An alias for --section-headers
  ....
```

**It seems that it doesn't work so simple**

I heard about this command in my 2nd year of college, but I rarely used it.
Most of the time I was like Homer in the picture below when I had to use `readelf`.

![Too many options](../images/too-many-options.png)

This year I started using it more, so I thought it would be good to give some practical examples and the most used options.

## Top 5 commands of `readelf`

### Display the ELF file header

`readelf -h <binary>`

```
~ > readelf -h ./exec_file
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Shared object file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x1040
  Start of program headers:          64 (bytes into file)
  Start of section headers:          14120 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         13
  Size of section headers:           64 (bytes)
  Number of section headers:         29
  Section header string table index: 28
```

Usually in this section we look for:

- Entry point address
- Start of section headers
- Type

You can find more information about ELF header [here](https://linux-audit.com/elf-binaries-on-linux-understanding-and-analysis/#elf-header).

### Display the program header table

`readelf -l <binary>`

```
~ > readelf -l ./exec_file
Program Headers:
  Type           Offset             VirtAddr           PhysAddr           FileSiz            MemSiz              Flags  Align
  PHDR           0x0000000000000040 0x0000000000000040 0x0000000000000040 0x00000000000002d8 0x00000000000002d8  R      0x8
  INTERP         0x0000000000000318 0x0000000000000318 0x0000000000000318 0x000000000000001c 0x000000000000001c  R      0x1
  LOAD           0x0000000000000000 0x0000000000000000 0x0000000000000000 0x00000000000005d8 0x00000000000005d8  R      0x1000
  LOAD           0x0000000000001000 0x0000000000001000 0x0000000000001000 0x00000000000001e5 0x00000000000001e5  R E    0x1000
  LOAD           0x0000000000002000 0x0000000000002000 0x0000000000002000 0x0000000000000158 0x0000000000000158  R      0x1000
   ....
```

This command helps us see the **segments**, not the **sections**.
I recommend this [video](https://www.youtube.com/watch?v=Ss2e6JauS0Y) on youtube that explains very well in the beginning the difference between them.
Also more information about the [program header table](https://linux-audit.com/elf-binaries-on-linux-understanding-and-analysis/#program-headers).

### Display the sections header

`readelf -S <binary>`

```
~ > readelf -S ./exec_file
There are 29 section headers, starting at offset 0x3728:

Section Headers:
  [Nr] Name              Type             Address           Offset Size              EntSize          Flags  Link  Info  Align
  [0]                   NULL             0000000000000000  00000000 0000000000000000  0000000000000000           0     0     0
  [1] .interp           PROGBITS         0000000000000318  00000318 000000000000001c  0000000000000000   A       0     0     1
  [2] .note.gnu.pr[...] NOTE             0000000000000338  00000338 0000000000000030  0000000000000000   A       0     0     8
  [3] .note.gnu.bu[...] NOTE             0000000000000368  00000368 0000000000000024  0000000000000000   A       0     0     4
  [4] .note.ABI-tag     NOTE             000000000000038c  0000038c 0000000000000020  0000000000000000   A       0     0     4
  [5] .gnu.hash         GNU_HASH         00000000000003b0  000003b0 0000000000000024  0000000000000000   A       6     0     8
   ....
```

Now we know also how to see all [sections](https://linux-audit.com/elf-binaries-on-linux-understanding-and-analysis/#elf-sections).
In this output we are usually interested in:

- Address
- Size
- Flags - shows us the permissions on each section

### Display the symbol table

`readelf -s <binary>`

```
Symbol table '.dynsym' contains 6 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterT[...]
     2: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND [...]@GLIBC_2.2.5 (2)
     3: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
     4: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMC[...]
     5: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND [...]@GLIBC_2.2.5 (2)

Symbol table '.symtab' contains 41 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS Scrt1.o
     2: 000000000000038c    32 OBJECT  LOCAL  DEFAULT    4 __abi_tag
     3: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
     4: 0000000000001070     0 FUNC    LOCAL  DEFAULT   14 deregister_tm_clones
     5: 00000000000010a0     0 FUNC    LOCAL  DEFAULT   14 register_tm_clones
     6: 00000000000010e0     0 FUNC    LOCAL  DEFAULT   14 __do_global_dtors_aux
     7: 0000000000004014     1 OBJECT  LOCAL  DEFAULT   24 completed.0
     8: 0000000000003df8     0 OBJECT  LOCAL  DEFAULT   20 __do_global_dtor[...]
     9: 0000000000001120     0 FUNC    LOCAL  DEFAULT   14 frame_dummy
    10: 0000000000003df0     0 OBJECT  LOCAL  DEFAULT   19 __frame_dummy_in[...]
    11: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS test.c
    12: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    ....
```

This is similar to `nm` command but with more information about the symbols.
It also includes the `.dynsym` section.

### Display the relocations

`readelf -r <binary>`

```
Relocation section '.rela.dyn' at offset 0x518 contains 8 entries:
  Offset          Info           Type           Sym. Value    Sym. Name + Addend
000000003df0  000000000008 R_X86_64_RELATIVE                    1120
000000003df8  000000000008 R_X86_64_RELATIVE                    10e0
000000004008  000000000008 R_X86_64_RELATIVE                    4008
000000003fd8  000100000006 R_X86_64_GLOB_DAT 0000000000000000 _ITM_deregisterTM[...] + 0
000000003fe0  000200000006 R_X86_64_GLOB_DAT 0000000000000000 __libc_start_main@GLIBC_2.2.5 + 0
000000003fe8  000300000006 R_X86_64_GLOB_DAT 0000000000000000 __gmon_start__ + 0
000000003ff0  000400000006 R_X86_64_GLOB_DAT 0000000000000000 _ITM_registerTMCl[...] + 0
000000003ff8  000500000006 R_X86_64_GLOB_DAT 0000000000000000 __cxa_finalize@GLIBC_2.2.5 + 0
```

I will not give more details about the relocation part, a future article dedicated to this will probably follow, but until then you can watch this [video](https://www.youtube.com/watch?v=E804eTETaQs).

## Extra

### Display all information

`readelf -a`

```
~ > readelf -a ./exec_file
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Shared object file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x1040
  Start of program headers:          64 (bytes into file)
  Start of section headers:          14120 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         13
  Size of section headers:           64 (bytes)
  Number of section headers:         29
  Section header string table index: 28

Section Headers:
  [Nr] Name              Type             Address           Offset       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000       0000000000000000  0000000000000000           0     0     0
  [ 1] .interp           PROGBITS         0000000000000318  00000318       000000000000001c  0000000000000000   A       0     0     1
  [ 2] .note.gnu.pr[...] NOTE             0000000000000338  00000338       0000000000000030  0000000000000000   A       0     0     8
  [ 3] .note.gnu.bu[...] NOTE             0000000000000368  00000368       0000000000000024  0000000000000000   A       0     0     4
  ....
```

### Display the contents of the indicated section as hexadecimal bytes

`readelf -x <section/address>`

```
~ > readelf -x .data ./exec_file

Hex dump of section '.data':
  0x00004000 00000000 00000000 08400000 00000000 .........@......
  0x00004010 0a000000                            ....
```

### Display the contents of the indicated section as ASCII characters

`readelf -p <section/address>`

```
~ > readelf -p .data ./exec_file

String dump of section '.data':
  [     9]  @
```
