# Linking
Linking is the process that resolves references to dependencies and calculates the exact memory address for a reference that the compiler is asking for.

Linking is split into static and dynamic linking.
## Static Linking (compile-time)
The process of creating **tags** and writing to the **Procedure Linkage Table (PLT)**
within the binary to tell the dynamic linker where to find dependencies.
### Tags
The instructions (think of it like a physical map) that tells there dynamic linker where to find these dependencies at run-time.

There are 2 of tagging: `rpath` and `runpath`, tabled by Gemini:

| **Feature**             | **rpath (Old)**                                    | **runpath (New)**                                                |
| ----------------------- | -------------------------------------------------- | ---------------------------------------------------------------- |
| **Priority**            | Highest (Overrides everything)                     | Lower (Checked after environment variables)                      |
| **Flexibility**         | Rigid / Hard to debug                              | Flexible / Easier to test                                        |
| **Dependency Upgrades** | Since hardcoded, requires a complete recompilation | Doesn't require recompilation and can essentially be hot-swapped |
### Procedure Linkage Table (PLT)
The reference that the binary will use to understand what it needs to do to "jump" to an external function.

It is used when the code references an external dependency. It just references the **Global Offset Table (GOT)**.
## Dynamic Linking (run-time)
The process of finding and creating memory addresses for external dependencies.
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