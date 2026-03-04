>Typescript: closest thing is `.d.ts` files
>Dart: barrel files + abstract classes; export statements act like headers

## My attempt at summarizing/paraphrasing header files
Header files are guards, macros, or abstract definitions of functions or structures, that the pre-processor will copy paste into my source files for the compiler to understand.

They are like `export ...` in dart barrel files, where the files don't just hold the implementation of things, but just where to find them and what to expect when you use them. It's like importing a file and using a function within the imported file. You can use the function by calling it and abiding to the contract given by the function definition, but you don't see how it works.
## An Analogy by Gemini (i wish i thought of this)
- Header files: The **menu** at a restaurant. It tells you what you can order and how much it costs (the contract)
- Source files: The **kitchen** at the restaurant. The actual cooking happens here (the implementation). As long as you abide by the contract defined in the menu, you will get your intended result.
- Linker (see [[compilation]]): The **waiter** at the restaurant. They take your order from the menu, go to the kitchen, and bring the food back to the table.
# Why Header Files Are Needed
- clang has a one-pass compiler (reads everything top-to-bottom once)
	- Modern languages have multi-pass compilers which draw an internal map for any reference, no matter where it's used or defined
- Header files provide definition upfront for the compiler to reference later when that code is used in source
- The compiler will **FAIL** if source tries to reference something external that wasn't defined before
	- Header files provide that definition
# Notes
- end in `.h`
- declarations for fns, macros, and data types

Function Interface - definition without logic
Macro definition - constants or code snippets
Structure Definition - struct, enum, typedef
Guards - prevent file from being included multiple times

## Compilation Flow
Preprocessor scans `#include` lines
- Copy/paste entire content into source file `.c` before compilation

### Standard vs User Defined Headers
- Standard: C lib standards. Angle Brackets
	- `#include <stdio.h>`
- User defined: You create. Double quotes
	- `#include "my_functions.h`

### Example
```c
// my_math.h (header file)
#ifndef MY_MATH_H  // Include Guard
#define MY_MATH_H

int add(int a, int b); // Function Prototype
#define PI 3.14159     // Macro

#endif
```
```c
// main.c (source file)
#include <stdio.h>
#include "my_math.h"

int main() {
	printf("Sum: %d", add(5, 3));
	return 0;
}
```
