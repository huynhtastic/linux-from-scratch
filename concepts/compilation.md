# Linking
Linking is the process that resolves references to dependencies and has 2 categories:
- Static Linking
- Dynamic Linking
## Static Linking
The process where the **static linker** bakes the external dependencies into the compiled binary. This results in a larger binary with some restrictions, but it ensures the binary has everything it needs to run.

This happens only at **compile time**.
## Dynamic Linking
The process of allowing the binary to dynamically resolve external dependencies in places outside of the binary.

It involves creating **tags**, writing to the **Procedure Linkage Table (PLT)**, and finally creating the **Global Offset Table (GOT)** with the ultimate goal of being able to find and execute external dependencies.
### Procedure Linkage Table (PLT)
The table that the binary will reference to find the instructions needed "jump" to an external function to use it.
It just references the **Global Offset Table (GOT)** which contain the actual "jump" instructions.
### Global Offset Table (GOT)
The table that the PLT references to point where in the host to be able to find the external dependencies.
#### Tags
The instructions (think of it like a physical map) that tells there dynamic linker where to find these dependencies at run-time. These are built by the **static linker** but used by the **dynamic linker**.

They may be created but not always used as the linker could be told to look at other places first.

There are 2 of tagging: `rpath` and `runpath`, tabled by Gemini:

| **Feature**             | **rpath (Old)**                                    | **runpath (New)**                                                |
| ----------------------- | -------------------------------------------------- | ---------------------------------------------------------------- |
| **Priority**            | Highest (Overrides everything)                     | Lower (Checked after environment variables)                      |
| **Flexibility**         | Rigid / Hard to debug                              | Flexible / Easier to test                                        |
| **Dependency Upgrades** | Since hardcoded, requires a complete recompilation | Doesn't require recompilation and can essentially be hot-swapped |
# Compilation & Execution Flow
At run-time, the CPU will:
1. See a reference to an external dependency
2. Stop running code located in the binary
3. Look in the PLT to find where it needs to look in the **Global Offset Table (GOT)**
4. Look in the GOT to find the actual memory address for the external dependency
	1. If the address the CPU is looking for exists, then it goes there and executes code
	2. If not, it wakes up the **dynamic linker** and run its code:
		1. Looks where the tags say where to look for these dependencies
			1. With `runpath`, it look through this checklist:
				1. **`LD_LIBRARY_PATH`** (The override)
				2. **`RUNPATH`** (The internal tag)
				3.  **`/etc/ld.so.cache`** (The system's standard list)
				4. **`/lib` and `/usr/lib`** (The last resorts)
		2. Load the dependencies into memory and calculates the memory address
		3. Writes the memory address into the GOT