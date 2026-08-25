# 🛠️ Module 04: Static Analysis with Ghidra

Ghidra is a software reverse engineering (SRE) framework developed by the National Security Agency (NSA). This module covers core Ghidra concepts, workspace management, and navigation techniques for static binary analysis.

---

## 1. Ghidra Basics

<img width="447" height="447" alt="images" src="https://github.com/user-attachments/assets/98e062bc-8858-4f53-854e-02105cae1689" />

Ghidra converts compiled binary executables back into assembly instructions and decompiled C-like pseudocode. Unlike dynamic analysis tools, Ghidra analyzes code without executing the target file.

### Primary UI Windows

<img width="803" height="598" alt="image11" src="https://github.com/user-attachments/assets/839973d1-db41-4952-a5b2-5afe68d2f4a8" />

<img width="1575" height="1148" alt="210265653-48b078ab-e7b6-4542-a6b5-9c305770762d" src="https://github.com/user-attachments/assets/b0a92b89-8738-4a9e-a70d-af9ee9e6e408" />

* **Listing Window:** Displays the raw assembly instructions, memory addresses, byte values, and annotations.
* **Decompiler Window:** Automatically converts assembly instructions into readable C-like pseudocode.
* **Symbol Tree:** Displays organized symbols including functions, imports, exports, and global variables.
* **Console:** Displays status logs, analyzer updates, and error messages.

---

## 2. Projects

<img width="1009" height="683" alt="ghidra-22862-1" src="https://github.com/user-attachments/assets/3740e2d4-80c0-4b36-904f-b87174a4a3ed" />


Ghidra organizes binary analysis files using **Projects**. A project contains imported binaries, data type archives, and saved progress state across sessions.

### Project Types
* **Non-Shared Project:** Used for local, single-analyst workflows.
* **Shared Project:** Used for multi-analyst collaboration, requiring a Ghidra Server for version control and check-in/check-out mechanics.

### Hands-on Example: Creating a Project & Importing a Binary
1. Open Ghidra and go to `File` ➔ `New Project`.
2. Select **Non-Shared Project** and click **Next**.
3. Choose a workspace directory and set the Project Name (e.g., `Crackme_Analysis`).
4. Import a binary: Press `I` or drag a file (e.g., `sample.exe`) into the Project Window.
5. Double-click `sample.exe` to launch the **CodeBrowser** application.

---

## 3. Auto Analysis

<img width="1912" height="781" alt="Module4Analyze" src="https://github.com/user-attachments/assets/a500f821-0149-43e7-b0d5-cf06f685c7b7" />

When a binary is first opened in CodeBrowser, Ghidra prompts to analyze the file automatically. Auto Analysis runs disassembly passes, identifies compiler patterns, recovers function signatures, and builds control flow graphs.

### Key Analysis Options
* **Decompiler Parameter ID:** Recovers function arguments and return types.
* **Windows Resource Reference:** Parses embedded resources inside Windows PE files.
* **Stack Analysis:** Analyzes local variable layouts and stack pointer movements.

### Hands-on Example: Running & Configuring Auto Analysis
1. Upon opening a binary in CodeBrowser, click **Yes** when asked: *"sample.exe has not been analyzed. Would you like to analyze it now?"*
2. Check or uncheck specific analyzers in the option panel (e.g., enable `Aggressive Instruction Finder` for heavily obfuscated code).
3. Click **Analyze**. Track progress via the blue loading bar at the bottom right corner of the CodeBrowser interface.

---

## 4. Symbol Tree

<img width="442" height="452" alt="images" src="https://github.com/user-attachments/assets/4cfda940-8ef7-418c-a8fc-b7b6e03b78fe" />

The **Symbol Tree** panel categorizes all labeled entry points, functions, variables, and external references found within the binary.

### Symbol Categories
* **Imports:** Functions called from external dynamic link libraries (e.g., `kernel32.dll!CreateFileA`).
* **Exports:** Functions made available by the binary for other applications (common in DLLs).
* **Functions:** User-defined or compiler-generated functions detected within the executable code segments.
* **Labels:** Specific memory locations assigned custom names by the analyst or decompiler.

### Hands-on Example: Locating Import Symbols
1. Locate the **Symbol Tree** window on the left side of CodeBrowser.
2. Expand the `Imports` folder.
3. Search for security-sensitive API calls, such as `IsDebuggerPresent` or `InternetOpenA`.
4. Double-click the symbol name to jump directly to its location in the **Listing Window**.

---

## 5. Functions

<img width="266" height="532" alt="images" src="https://github.com/user-attachments/assets/52a061c9-e38a-40a2-bf79-acca7bcc19f0" />

Ghidra automatically groups contiguous assembly instructions into discrete function structures, allocating local stack space and determining calling conventions.

### Reversing Functions

* **Renaming Functions:** Default functions are named `FUN_<address>` (e.g., `FUN_004012a0`). Renaming improves code readability.
* **Editing Signatures:** Overriding argument types and return values forces the decompiler to output clean pseudocode.

### Hands-on Example: Renaming a Function
1. Navigate to a function in the Decompiler view:
```c
// Before Renaming
undefined4 FUN_00401000(int param_1) {
    if (param_1 == 0x1337) {
        return 1;
    }
    return 0;
}

```

1. Click on FUN_00401000, press N, and enter validate_license_key.

2. Right-click the function name and select Edit Function Signature to adjust parameters:

```C
// After Renaming and Re-typing
bool validate_license_key(int user_key) {
    if (user_key == 0x1337) {
        return true;
    }
    return false;
}
```
## 6. Strings

<img width="278" height="181" alt="images" src="https://github.com/user-attachments/assets/ae2535cf-3db0-402b-9742-21100a43e5d9" />

Strings embedded in binaries often reveal critical context, such as status messages, hardcoded passwords, server URLs, encryption keys, and file paths.

#### String Search Workflow
Ghidra parses data sections (.rdata, .rodata, .data) to identify ASCII and Unicode string sequences.

Hands-on Example: Searching and Defining Strings

1. In the top navigation bar, click Search ➔ For Strings....

2. Keep default settings (Minimum length: 5) and click Search.

3. Filter the results table for key phrases like "Access Granted", "Password:", or "http://".

4. Double-click a search result row to navigate to its memory location in the Listing Window.

5. If Ghidra fails to auto-format a string, select the bytes in the Listing window and press T to define it manually as a string.

## 7. References (Xrefs)

<img width="478" height="418" alt="images" src="https://github.com/user-attachments/assets/b28aa6b4-2e72-4270-86fd-400108ddd007" />


Cross-references (Xrefs) link data, strings, and functions to the specific instructions that read, write, or call them. They answer the question: "Where in the code is this item used?"

#### Reference Types

* Call Reference (c): An instruction calls a function (e.g., CALL FUN_00401000).

* Read/Write Reference (r/w): An instruction reads or updates a global memory variable.

* Data Reference: An instruction loads the memory address of a string or buffer.

#### Hands-on Example: Tracing String References to Logic Checks

1. Locate a string in memory (e.g., "Access Denied" at address 0x00405010).

2. Look at the right-hand side of the Listing window line for the XREF header, or press Ctrl + Shift + F on the string label.

3. Observe the reference tag:

```Code snippet
00405010  db  "Access Denied", 00
; XREF[1]:  validate_serial+0x42(R)

```
1. Double-click validate_serial+0x42 to navigate directly to the exact conditional branch instruction using that error message.

```C
// Decompiler view showing where the reference landed
if (check_serial(input_buffer) == 0) {
    puts("Access Denied"); // Xref points here
}

```

# 🛠️ Module 04: Advanced Decompilation & Code Recovery with Ghidra

This section covers the core reverse engineering workflow in Ghidra: utilizing the decompiler to reconstruct program structures, recovering variables and functions, analyzing control flow, and defining custom data structures.

---

## 1. Decompiler Usage

The Ghidra Decompiler translates low-level assembly language into high-level C pseudocode. It continuously updates its output in real-time as you modify data types, symbol names, and function parameters.

### Key Decompiler Interactions

* **Navigation:** Double-clicking any variable, function, or address inside the Decompiler jumps to its definition or cross-reference location.
* **Synchronized Highlighting:** Clicking an instruction in the Listing view highlights its corresponding pseudocode statement in the Decompiler view (and vice versa).
* **Decompiler Options:** Accessible via `Edit` ➔ `Tool Options...` ➔ `Decompiler` to adjust code formatting, field display, and alias display settings.

---

## 2. Variable Recovery
When compiling source code, high-level variable names are stripped, leaving behind raw memory references (stack offsets or CPU registers). Ghidra attempts to reconstruct these local variables and function parameters during analysis.

### Variable Types in Ghidra
* **Stack Variables (`local_XX`):** Memory slots located relative to the stack pointer (e.g., `[RBP - 0x10]`).
* **Register Variables:** Values held purely inside registers (e.g., `RAX`, `ECX`) across instruction blocks.
* **Global Variables (`DAT_XX`):** Statically allocated variables residing in the `.data` or `.bss` memory sections.

### Hands-on Example: Re-typing & Fixing Variable Sizes

1. Observe raw decompiler output where a variable is misidentified:
```c
// Before Variable Recovery
undefined4 FUN_00401120(int param_1) {
    undefined4 local_10;
    local_10 = 0x5f3759df;
    return local_10;
}
```
1. Click on local_10, press L (Set Data Type), and change its type from undefined4 to float.

2. Press L on param_1 and change its type to float.

// After Variable Recovery
```
float FUN_00401120(float input_val) {
    float magic_const;
    magic_const = 0x5f3759df;
    return magic_const;
}
```

## 3. Function Recovery

<img width="240" height="210" alt="images" src="https://github.com/user-attachments/assets/ef998fdb-71f0-43a3-b02e-e57f1a75cf11" />

During compilation, function names are replaced with memory addresses. Ghidra detects function boundaries (prologues and epilogues) and reconstructs their signatures (return types and argument lists).

#### Recovery Techniques

* Calling Convention Analysis: Identifying how arguments are passed (e.g., via stack for cdecl or via registers RCX, RDX, R8, R9 for Windows x64).

* Custom Signatures: Overriding automatic detection when Ghidra misidentifies parameter counts.

### Hands-on Example: Fixing Function Signatures

1. Select the function header in the Decompiler view.

2. Right-click and choose Edit Function Signature (or press F).

3. Update return types, parameters, and calling conventions manually:

```C
// Unrecovered Function Signature
undefined4 FUN_00401500(undefined4 param_1, undefined4 param_2);

// Reconstructed Signature
int authenticate_user(char *username, char *password_hash);

```
## 4. Control Flow Analysis

Control flow analysis helps map execution paths using loops, conditional branches, and function calls.

#### Control Flow Artifacts

* Conditional Branches: if/else statements parsed from CMP, TEST, and JMP instructions.

* Loops: while, for, and do-while loops recovered from backward jump instructions.

* Switch Statements: Jump tables converted into switch(x) { case ... } blocks.

Hands-on Example: Analyzing Branch Logic & Control Flow Graphs

1. Navigate to a function in the Listing view.

2. Click the Display Function Graph icon in the toolbar (or menu Window ➔ Function Graph).

3. Examine conditional logic visually:

* Green Arrow: Branch taken (JZ / JE condition evaluated to true).

* Red Arrow: Branch not taken (fall-through execution).

4. Identify loop back-edges to locate key processing logic (e.g., decryption or hashing loops).

## 5. Renaming (Functions & Variables)

Giving meaningful names to auto-generated symbols (FUN_, DAT_, local_) transforms unreadable pseudocode into self-documenting code.

#### Keyboard Shortcuts

N — Rename any selected variable, function, label, or field.

#### Hands-on Example: Sequential Renaming Workflow

1. Select FUN_00401200 and press N ➔ Rename to decrypt_payload.

2. Inside decrypt_payload, select local_14 and press N ➔ Rename to key_index.

3. Select DAT_00407020 and press N ➔ Rename to g_XorKey.

```C
// Fully Renamed Pseudocode
void decrypt_payload(char *buffer, int buffer_len) {
    int key_index;
    for (key_index = 0; key_index < buffer_len; key_index++) {
        buffer[key_index] ^= g_XorKey[key_index % 4];
    }
}
```

## 6. Structures

Binaries frequently use C structs (e.g., custom user objects, complex headers, network packets). By default, Ghidra shows accesses to structure fields as primitive offset calculations (e.g., *(param_1 + 0x10)). Reconstructing structures turns offset arithmetic into named field accesses.

#### Creating Structures in Ghidra

1. Open the Data Type Manager window (Window ➔ Data Type Manager).

2. Right-click your binary project file entry ➔ Select New ➔ Structure.

3. Define the structure name, field types, and field names.

Hands-on Example: Defining and Applying a Custom Structure
Suppose a binary accesses a buffer using pointer offsets:

```C

// Raw Decompiler View (Without Structure)
void process_user(longparam_1) {
    if (*(int *)(param_1 + 0x0) == 1) {
        printf("Admin: %s\n", (char *)(param_1 + 0x4));
    }
}
```

#### Step 1: Define Structure in Structure Editor

Create a structure named USER_ACCOUNT:

Offset Data-Type Field -Name0x00intis_admin0x04char[32]username0x24uintuser_id

#### Step 2: Apply Structure to Pseudocode

1. In the Decompiler, click on param_1.

2. Press L (Set Data Type) and type USER_ACCOUNT *.

3. The Decompiler automatically updates the offset logic:

```C
// Decompiler View (With Applied Structure)
void process_user(USER_ACCOUNT *user_ptr) {
    if (user_ptr->is_admin == 1) {
        printf("Admin: %s\n", user_ptr->username);
    }
}
```

The Decompiler automatically updates the offset logic:
