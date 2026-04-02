# fastlang
the fastmeme programming language
ive been slowly honing this language for 13 years on minimum wage without the help of a company or an engineering degree to help me.

mcr:

mcr stands for macro. it pipes posix shell into posix shell. the intended use is like so. the the right hand sh is meant to evaluate raw sh code unedited. the left hand sh is meant to host any process that takes an implementation of a language that compiles sh. 

in the lefthand sh one may use a loop with the read function to halt the evaluation of sh code for the total absorbtion of all stdin into a file. then one may use awk to filter that file, expecting source code in one languahe such as foo, and mapping syntax in foo to semmantics in sh. sh code may be printed by awk for evaluation.

the problem with using awk to transform the syntax is that awk programs are not dynamic at runtime. so I have made two interpreters with flexible syntax at runtime, one in awk, and a much faster one in c. these are called awk_vm, and vm on my gifhub.

tcjit: 

this allows you to just in time compile a c file on the fly. it is just a shim around tcc so that the source file does not have to exit ram. perisistent memory across tcjit processes can be done with the ipc protocol mmap.

elfc:

i wrestle with myself if elfc is actually a necessary component, or if elfc should be implemented as an mcr macro. elfc is kind of a guideline, a very loose specification, for how to turn c into high level assembler language.

if an entire line of elfc code js just "c" this toggles on and off the c mode which lets you write inline c and thus inline assembler. otherwise you are somewhat forced to write elfc code which is intended to be a subset of standard posix compliant c that can be injected directly anywhere into a c file. this subset feels much like an asm language

it is recommended that if a function in c is to contain elfc code, that the code within the codeblock contain nothing but elfc code plus a return statement. it is recommended not to mix elfc code with c code in functions.

the beauty of elfc code is that pure elfc code has no side effects except for writing to mmaped memory.

elfc code is meant to hav zero safeties or error checking that an elf file in posix doesnt have. so yes it is unsafe but so is elf. yes it is difficult to debug but sonis elf.

"defmap" - is an op code that uses a c macro to define wrappers for four core memory map functions - mmap, munmap, fopen, and memcpy. the reason for this is that on android, windows, and ios, there are different function names for these with different function paramaters. this one line is intended to be able to be changed so that the code will work across those oses.

"elfc_open" - open a file with a filedescriptor

"elfc_map" - memory map some memory and get the pointer to that memory. the user is advised to use the entire space of memory for nothing but ints. the user is advised to use int as char. the user is advised to use ints as locations of how how far to advance a pointer rather than to save pointers in the mmaped space. the user is advised to perform division as integer division with a remainder rather than floating point division. mmap is how elfc processes perisist memory after death, and do inter process comminication. the user is advised to memory map in chunks of 64 MB (???) for windows compatibility.

"elfc_unmap" - free a memory mapped region

"elfc_memcpy" - rapidly copy n bytes from one array to another

"var" - declare a variable. the user is advised to only declare 32 variables: int elfc_int_register_0, elfc_int_register_1, ... elfc_int_register_15; void *elfc_pointer_register_0, *elfc_pointer_register_1,... elfc_pointer_register_15; only 16 registers might be needed because unlike in a processor most registers dont need singular special purposes. keeping the variables to these 32 registers simplifies specification. elfc_pointer_register_0 is suggested to hold the global pointer to memory. elfc_int_register_15 is suggested to hold the condition for a loop. elfc_int_register_14 is suggested to hold the state of a switch.it is suggested that all registers be local variables so tha multiple c functions do not have to share registers in the same process.

"loop" - this begins the declaration of a loop. it is suggested to loop over the condition stored in elfc_int_register_15. it is suggested to have only one loop per elfc function.

"switch" - this begins a switch for a finite state machine. it is suggested to switch over the condition stored in elfc_int_register_14. it is suggested to have only one switch per elfc function.

"case" - begin a case for a switch statement. these are used as methods within elfc functions. it is suggested to case over an int literal or character literal.

"break" - jump to the first line after the current switch statement

"end" - end function, switch, or loop

"size" - get the size of a c object. this is useful for pointer manipulation.

"op" - example : op elfc_int_register_0 elfc_int_register_0 + elfc_int_register_7. example : op elfc_int_register_0 elfc_int_register_0 && elfc_int_register_7

"get" - dereference the data inside of a pointer. fetch some data a pointer points to.

"set" - store some data inside of a pointer at the location it points to.

"seek" - manipulate a pointer by advancing n bytes.

***

elfcjit is a program intended to be defined in the righthand sh of mcr. the idea is that with elfc jit you can swap between interpreting sh or jitting elfc code
