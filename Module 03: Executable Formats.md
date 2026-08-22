
## In this Module we get to know about the most important PE and ELF file format 

How this module helps you ? 

Understanding executable formats like PE and ELF is the foundation of **Reverse Engineering**. When you open a compiled binary in a disassembler or a debugger (such as Ghidra, IDA Pro, or Binary Ninja), you aren't just looking at random assembly code; you are looking at data organized by these exact specifications.

Here is how mastering this module directly translates to reverse engineering skills:

---

## 1. Mapping the "Layout of the Land"

Before you can analyze a single line of code, you have to know where to look.

* **The Entry Point:** The **NT Header** (Windows) or **ELF Header** (Linux) tells you exactly where the program starts execution. Malware analysts use this to jump straight to the beginning of the program's actual logic, skipping boilerplate setup code.
* **Section Permissions:** Recognizing section headers helps you identify what data can do what. If you find code executing inside a `.data` section (which should normally only hold variables), it is a major red flag indicating potential runtime code injection or a custom packer/crypter.

## 2. Speed-Running Malware Analysis via Imports

You can often guess exactly what a program does before looking at its assembly language instructions just by examining its **Imports (IAT)**.

* If a binary imports `InternetOpenA` and `HttpSendRequestA` from `wininet.dll`, you instantly know it communicates over the web.
* If it imports `CreateToolhelp32Snapshot`, `Process32First`, and `OpenProcess`, it is actively scanning your computer's running processes.

## 3. Spotting Packed, Encrypted, or Protected Binaries

Malware authors and software developers use "packers" (like UPX) to compress or obscure their code to hide from reverse engineers. Understanding headers allows you to spot this immediately:

* **Abnormal Section Names:** Seeing sections named `UPX0` or `.pack` instead of `.text` tells you the file is compressed.
* **Imbalanced Ratio:** If a file's disk size is tiny, but its `VirtualSize` (the space it requests in RAM via the Section/Program Headers) is massive, it means the binary is going to unpack and extract hidden code into memory during runtime.

## 4. Uncovering Secrets in the Resources (`.rsrc`)

Attackers often hide payloads inside the Resources section of an innocuous-looking executable.

* A reverse engineer will check the `.rsrc` section for uncommonly large raw data blocks.
* Advanced malware might look like a simple calculator app, but its resource section could hide an entire encrypted malicious `.exe` or `.dll` that it drops and runs later.

## 5. Bypassing Anti-Analysis and Hooking (Dynamic Linking)

By understanding how the **PLT/GOT** (in ELF) or the **IAT** (in PE) route function calls, you can manipulate how a program behaves.

* **API Hooking:** Reverse engineers can rewrite the addresses inside the Import Address Table or Global Offset Table to redirect function calls to their own custom analysis tools.
* For example, you can hook a `CheckLicenseKey()` function call and force it to return `True`, effectively bypassing an application's registration check.


---

# 1.PE File Format 

The **PE (Portable Executable) format** is the file structure used by Windows for executables (`.exe`), dynamic link libraries (`.dll`), control panel extensions (`.cpl`), and more. It dictates how the Windows OS loader maps the file into memory so it can be executed.

Here is a breakdown of each core component you highlighted, along with intuitive examples of what they do.

### Architecture of .exe files

<img width="401" height="686" alt="images" src="https://github.com/user-attachments/assets/92206ec5-f24c-45e8-8a6c-fbc78ab592d9" />

### This picture shows the real binary example of .exe files using ( Binary Ninja Tool )

<img width="650" height="596" alt="PE Headers annotated" src="https://github.com/user-attachments/assets/6a77ea95-caf1-475f-8fd2-564d79327835" />

---

## 1. DOS Header

Every PE file starts with a legacy **DOS Header** (`IMAGE_DOS_HEADER`). It exists purely for backwards compatibility with MS-DOS.

* **How it works:** It tells an ancient DOS system that this program cannot run in DOS mode. It also contains a vital pointer at the very end (`e_lfanew`) that tells the modern Windows loader where the real PE header starts.
* **Key Magic Bytes:** It always starts with the ASCII characters **`MZ`** (named after Mark Zbikowski, a key MS-DOS developer).
* **Example:** If you try to run a modern 64-bit Windows game inside a 1980s MS-DOS emulator, the DOS header executes a tiny built-in DOS program that simply prints:
> `"This program cannot be run in DOS mode."`



## 2. NT Header

The **NT Header** (`IMAGE_NT_HEADERS`) is where the modern Windows-specific execution details live. It contains three main parts: the Signature, the File Header, and the Optional Header.

* **Signature:** A 4-byte signature verifying it's a valid PE file (ASCII **`PE\0\0`**).
* **File Header:** Contains physical traits of the file (e.g., whether it's built for x86 or x64 architecture, and how many sections it has).
* **Optional Header:** Don't let the name fool you—it is **mandatory** for executable files. It defines how the file is loaded into memory, including the **AddressOfEntryPoint** (the exact memory address where execution starts) and the size of the stack/heap.
* **Example:** When you double-click `calc.exe`, the OS loader checks the NT Header to see: *"Is this a 64-bit binary? Yes. Where is the first instruction? At address `0x140001000`."*

## 3. Sections

A PE file is split into distinct logical blocks called **Sections**. Each section holds a specific type of data and has its own memory permissions (Read, Write, Execute).

* **`.text` (or `.code`):** Contains the actual CPU instructions. (Permissions: **Read / Execute**)
* **`.data`:** Contains initialized global and static variables. (Permissions: **Read / Write**)
* **`.rdata`:** Contains read-only initialized data, like literal strings. (Permissions: **Read Only**)
* **Example:**
```c
int global_var = 42;          // Lives in .data (can be read and changed)
const char* msg = "Hello!";   // The characters "Hello!" live in .rdata
void main() { int x = 1; }    // The compiled assembly for main lives in .text

```



## 4. Imports (Import Address Table - IAT)

Most programs don't write everything from scratch; they rely on APIs provided by Windows DLLs (like handling windows, playing sounds, or reading files). The **Imports** section lists every external function the binary needs to borrow.

* **How it works:** The PE file lists the DLL name and the functions it wants. When the program runs, the Windows loader locates those DLLs in memory and fills out the **Import Address Table (IAT)** with the actual memory addresses of those functions.
* **Example:** A ransomware binary or a basic internet downloader will have imports pointing to `URLDownloadToFileW` inside `urlmon.dll` and `CreateFileW` inside `kernel32.dll`.

## 5. Exports (Export Data Directory)

If Imports are what a file *takes*, **Exports** are what a file *gives*. This section is predominantly found in **DLLs**. It lists the functions that this file makes publicly available for *other* programs to use.

* **How it works:** It maps function names or serial numbers (ordinals) to their exact location inside the file's `.text` section.
* **Example:** Windows' `kernel32.dll` has an Export table listing thousands of functions, such as `CreateProcess`, `VirtualAlloc`, and `Sleep`, so that your applications can call them.

## 6. Resources

The **Resources** section (usually named `.rsrc`) acts like a built-in file system inside the executable. It holds UI elements and assets that the application needs but aren't raw code.

* **What lives here:** Icons, custom mouse cursors, user interface menus, localized strings for different languages, manifest files, and even embedded audio files or secondary binaries.
* **Example:** When you look at an `.exe` file on your desktop and see its custom application icon (like the yellow folder for a zipped file or the chrome wheel), Windows is reading that picture directly out of the file's **Resources** section without executing the code.

## 7. Relocations (Base Relocations)

When a compiler builds an executable, it assumes the program will be loaded into a specific preferred spot in memory (the Base Address). However, due to security features like **ASLR (Address Space Layout Randomization)**, Windows almost always loads the file at a random memory location to prevent exploits.

* **How it works:** If a program is shifted to a different address than expected, all hardcoded memory references in the code break. The **Base Relocation Table** (`.reloc`) is a list of every single spot in the code where a hardcoded memory address was used.
* **Example:** If the code says *"Jump to address `0x00401050`"*, but ASLR moved the whole program forward by `0x100000` bytes, the loader uses the relocation table to find that instruction and fix it on the fly to *"Jump to address `0x01401050`"*.
---

# 2.ELF Format 

### This shows the Architecture of ELF Format

<img width="424" height="471" alt="images" src="https://github.com/user-attachments/assets/ae2d958a-9fd5-4502-b85d-61c61f81cd9e" />

### This is the ELF files binary visualizations 

<img width="840" height="522" alt="annotated_elf_1" src="https://github.com/user-attachments/assets/5465fdbc-9fdc-4669-a9aa-4b52a2b648ef" />


The **ELF (Executable and Linkable Format)** is the standard binary file format used by Linux and other Unix-like operating systems for executables, shared libraries (`.so`), and object files (`.o`). It serves the exact same purpose on Linux that the PE format serves on Windows.

Here is a breakdown of the core components of an ELF binary, explained with practical examples.

---

## 1. ELF Header

The **ELF Header** (`Elf32_Ehdr` / `Elf64_Ehdr`) sits at the very beginning of the file. It serves as a roadmap, telling the operating system exactly how to read and interpret the rest of the binary.

* **Key Magic Bytes:** Every valid ELF file must start with the exact 4-byte sequence: **`0x7F 'E' 'L' 'F'`** (or `7f 45 4c 46` in hex).
* **What it contains:** It holds structural metadata, such as:
* Whether the binary is **32-bit** or **64-bit**.
* The **Target Architecture** (e.g., x86-64, ARM, MIPS).
* The **Entry Point** (the exact virtual memory address where the CPU should start executing code).
* The file offsets where the Section Headers and Program Headers begin.


* **Example:** When you type `./my_program` in a Linux terminal, the kernel reads the ELF Header first. If it sees an ARM architecture signature on an x86-64 Intel laptop, it instantly throws an error: *"Binary file cannot be executed."*

---

## 2. Sections

**Sections** hold the bulk of the file’s data used during compilation and linking, but they are primarily intended for linkers (like `ld`) rather than the active runtime loader.

Common standard sections include:

* **`.text`:** The actual compiled machine code (CPU instructions).
* **`.data`:** Initialized global and static variables that can be modified at runtime.
* **`.rodata`:** Read-only data, such as hardcoded string constants.
* **`.bss`:** Uninitialized global variables. Instead of wasting space on disk storing zeros, this section simply tells the OS: *"Reserve this much space for zeros in memory when launching."*
* **Example:**
```c
int status = 1;             // Stored in .data
int buffer[1000];           // Stored in .bss (takes up almost 0 bytes on disk)
const char* flag = "WIN";   // Stored in .rodata
void run() { int x = 5; }   // Assembly instructions stored in .text

```



---

## 3. Program Headers (Segments)

While *Sections* are for compilers and linkers, **Program Headers** are mandatory for execution. They tell the Linux kernel how to map the file into virtual memory segments. One segment typically groups multiple sections together based on what permissions they need.

* **How it works:** The Program Headers specify blocks of data, their file offsets, their target memory addresses, and their strict runtime flags: **Read (R)**, **Write (W)**, and **Execute (X)**.
* **Common Segments:**
* `PT_LOAD`: Tells the kernel to physically map this chunk of the file into RAM. Usually, one `PT_LOAD` segment is **R-X** (for code) and another is **RW-** (for data).
* `PT_INTERP`: Specifies the path to the string of the dynamic linker/loader (usually `/lib64/ld-linux-x86-64.so.2`).


* **Example:** A Program Header tells Linux: *"Take bytes from offset `0x1000` to `0x2000`, load them at memory address `0x400000`, and make it Strictly Execute-Only. If the program tries to write data here, crash it immediately with a Segmentation Fault."*

---

## 4. Dynamic Linking

Most Linux programs don't contain every single line of code they need to run. Instead, they rely on shared system libraries (like `libc.so` for standard C functions). **Dynamic Linking** is the mechanism that wires these external dependencies together at runtime.

* **How it works:** The ELF binary utilizes two primary structures:
* **`.dynamic` section:** Acts as a directory containing tags like `DT_NEEDED`, which lists the names of required shared objects (e.g., `libc.so.6`).
* **PLT and GOT (Procedure Linkage Table / Global Offset Table):** These work together as a redirection system. When your code calls an external library function, it actually jumps to a stub in the PLT. The PLT checks the GOT to see if the real address of the function has been resolved yet. If not, it invokes the dynamic linker to look up the library function, patch its actual memory address into the GOT, and execute it.


* **Example:** If your code calls `printf("Hello");`, it doesn't contain the code for `printf`. Instead, the binary's dynamic structures point to `libc.so`. The first time `printf` is hit, the runtime linker locates `libc`, grabs the real address of `printf`, and wires it straight into your running process.

---

### Difference Between PE and ELF 

| Feature | PE Format (Windows) | ELF Format (Linux/Unix) |
| :--- | :--- | :--- |
| **Primary OS** | Microsoft Windows | Linux, Android, macOS (historically), BSD |
| **Magic Bytes** | `MZ` (at the very start) / `PE\0\0` | `\x7F ELF` |
| **Legacy Baggage** | Includes an MS-DOS Stub for 1980s backwards compatibility. | Clean; starts straight with system metadata. |
| **Dynamic Libraries** | Relies on `.dll` files. | Relies on `.so` (Shared Object) files. |
| **External Imports** | Maps directly to a fixed Import Address Table (IAT). | Uses an indirect PLT (Procedure Linkage Table) and GOT (Global Offset Table) loop. |
| **Base Relocation** | Mandated via the `.reloc` section to support ASLR security. | Often relies on PIC (Position Independent Code), minimizing the need for heavy relocation tables. |
