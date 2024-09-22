Report: Exploring the C Compilation Process

Program Overview
The C program consists of three files:
- main.c: Contains the main function, which calls a utility function square to compute the square of an integer.
- math_utils.c: Implements the square function.
- math_utils.h: Declares the square function.


Compilation Steps:
Compile source files into object files:
     gcc -c main.c
   gcc -c math_utils.c
      This generates main.o and math_utils.o.

Link the object files to create the executable:
     gcc main.o math_utils.o -o square_prog
      This generates the executable square_prog.

Symbol Analysis Using nm

### Symbols in main.o:nm main.o
Output:                 U printf
0000000000000000 T main
                 U square
- U indicates undefined symbols: printf and square are not defined in main.o but are used.
- T indicates a symbol defined in the text (code) section: main is defined here.

### Symbols in math_utils.o:nm math_utils.o
Output:0000000000000000 T square
- T indicates that the square function is defined in this object file.

### Symbols in square_prog:nm square_prog
Output:                 U printf
0000000000001129 T main
0000000000001100 T square
- The executable still has an undefined reference to printf because it relies on the system's C library for the actual implementation. Both main and square are defined here after linking.

Assembly Analysis Using objdump

### Assembly of main.o:objdump -d main.o
Relevant output:0000000000000000 <main>:
   0:   55                      push   %rbp
   1:   48 89 e5                mov    %rsp,%rbp
   ...

### Assembly of math_utils.o:objdump -d math_utils.o
Relevant output:0000000000000000 <square>:
   0:   55                      push   %rbp
   1:   48 89 e5                mov    %rsp,%rbp
   ...

Assembly of square_prog:objdump -d square_prog
The disassembled code of the executable includes the machine code for both the main and square functions, combined into a single binary. The assembly code remains mostly unchanged from the object files, with the main difference being that the addresses and offsets in the final executable are resolved.

ELF Structure Analysis Using readelf

### ELF Header of main.o:readelf -h main.o
Relevant output:Type:   REL (Relocatable file)
Machine:   Advanced Micro Devices X86-64
- This is a relocatable file, meaning it can be linked with other object files.

### ELF Header of square_prog:readelf -h square_prog
Relevant output:Type:   EXEC (Executable file)
Machine:   Advanced Micro Devices X86-64
- This indicates that square_prog is a fully linked executable.

### Section Headers Analysis:readelf -S main.o
Output shows various sections like .text (code), .data (initialized data), and .bss (uninitialized data). Similarly, running readelf -S square_prog reveals additional sections like .got and .plt, which handle dynamic linking.

Comparison of Outputs


- In main.o, the square function is marked as undefined (U), as it is declared in math_utils.h but defined in math_utils.o.
- After linking, both main and square are defined in the executable.### Sections:
- Object files (main.o and math_utils.o`) are relocatable with .text`, .data, and .bss sections.
- The executable (square_prog) has additional sections for handling dynamic linking, such as .got (Global Offset Table) and .plt (Procedure Linkage Table).

Role of Linking:
- The linker resolves the references to undefined symbols, combining object files into a single executable with all symbols defined.
