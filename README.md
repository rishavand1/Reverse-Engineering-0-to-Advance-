# 🔍 Reverse Engineering: Zero to Hero (0-to-Hero)

An exhaustive, self-paced roadmap designed to take security enthusiasts, researchers, and developers from basic computer fundamentals to advanced malware analysis, vulnerability research, and binary exploitation.

---

## 🌟 Key Benefits of Consuming This Repository

* **Zero-to-Hero Learning Arc:** Structured systematically to eliminate guesswork. Moves seamlessly from basic OS/C concepts into low-level assembly, binary analysis, and advanced reverse engineering.
* **Hands-on & Lab Focused:** Every theory phase is complemented by real-world crackmes, laboratory environments, and practical reverse engineering challenges.
* **Cross-Platform Expertise:** Provides comprehensive training across major operating systems including **Windows**, **Linux**, and **Android**.
* **Industry-Standard Tooling:** Teaches core principles alongside tools used by professional security researchers (**Ghidra**, **x64dbg**, **IDA Pro**, **Frida**, **Radare2**, and **GDB**).
* **Career-Aligned Curriculum:** Designed to build the exact skill set needed for roles in Malware Analysis, Security Research, Threat Intelligence, and Binary Exploitation.

---

## 🗺️ Detailed Curriculum Overview

### Phase 1: Core Foundations
* **[Module 00: Computer Fundamentals](Module%200%20:%20Computer%20Fundamentals.md)** — CPU architectures, memory layout (stack, heap, code, data), registers, instruction execution cycle, and endianness.
* **[Module 01: Programming for Reverse Engineers](Module%2001:%20Programming%20for%20Reverse%20Engineers.md)** — C/C++ mechanics, low-level memory management, pointers, structure alignment, and system calls.
* **[Module 02: Assembly Language](Module%2002:%20Assembly%20Language.md)** — Deep dive into x86, x64, and ARM instruction sets, stack frames, and calling conventions (`cdecl`, `stdcall`, `fastcall`).
* **[Module 03: Executable Formats](Module%2003:%20Executable%20Formats.md)** — Internal mechanics of Portable Executable (PE), Executable and Linkable Format (ELF), and Dalvik Executable (DEX).

### Phase 2: Static & Dynamic Analysis
* **[Module 04: Static Analysis with Ghidra](Module%2004:%20Static%20Analysis%20with%20Ghidra)** — Decompiler workflow, symbol recovery, function signature matching, and Ghidra headless scripting.
* **[Module 05: Dynamic Analysis](Module%2005:%20Dynamic%20Analysis)** — Live debugging, software/hardware breakpoints, instruction tracing, and memory inspection.
* **[Module 06: Windows Internals](Module%2006:%20Windows%20Internals)** — Process creation, thread contexts, memory protection, Win32 APIs, PEB/TEB structures, and handles.
* **[Module 07: Beginner Crackmes](Module%2007:%20Beginner%20Crackmes)** — Hands-on binary patching, serial key generation, and bypass logic.
* **[Module 08: Intermediate Reverse Engineering](Module%2008:%20Intermediate%20Reverse%20Engineering)** — Reversing custom cryptographic routines, obfuscated data structures, and state machines.
* **[Module 09: Malware Analysis Fundamentals](Module% 09:%20Malware%20Analysis%20Fundamentals)** — Triage methodology, behavioral analysis, registry monitoring, and extracting indicators of compromise (IOCs).

### Phase 3: Platform Specialization
* **[Module 10: Android Reverse Engineering](Module%2010%20:%20Android%20Reverse%20Engineering)** — Decompiling APKs, Smali bytecode analysis, dynamic instrumentation using Frida, and JNI reversing.
* **[Module 11: Linux Reverse Engineering](Module%2011:%20Linux%20Reverse%20Engineering)** — System call analysis, ELF stripping recovery, and GDB dynamic analysis with PEDA/GEF.
* **[Module 12: Advanced Static Analysis](Module%2012:%20Advanced%20Static%20Analysis)** — Decompiler optimization patterns, custom plugin creation, and type propagation.
* **[Module 13: Advanced Dynamic Analysis](Module%2013:%20Advanced%20Dynamic%20Analysis)** — Kernel-level debugging, hypervisor setups, API hooking, and automated execution tracing.

### Phase 4: Advanced Topics & Exploitation
* **[Module 14: Anti-Analysis Techniques](Module%2014:%20Anti-Analysis%20Techniques)** — Defeating anti-debugging, anti-disassembly, anti-VM, and unpacking custom packers.
* **[Module 15: Vulnerability Research](Module%2015:%20Vulnerability%20Research)** — Root cause analysis, memory corruption, stack/heap overflows, and Return-Oriented Programming (ROP).
* **[Module 16: Advanced Malware Analysis](Module%2016:%20Advanced%20Malware%20Analysis)** — Reversing ransomware, rootkits, APT toolkits, and analyzing command-and-control protocols.
* **[Module 17: Recommended Platforms](Module%2017:%20Recommended%20Platforms)** — Practice platforms, CTF challenges, and malware repositories.
* **[Module 18: Crackmes Progression](Module%2018:%20Crackmes%20Progression)** — A curated, difficulty-ranked challenge list.
* **[Module 19: Final Capstone Projects](Module%2019:%20Final%20Capstone%20Projects)** — Real-world analysis assignments to build your professional portfolio.

---

## 🛠️ Prerequisites & Setup

Before getting started:
1. Basic understanding of computer systems and programming logic.
2. Install a virtualization platform (**VMware Workstation** or **VirtualBox**) to build an isolated analysis sandbox.
3. Install core analysis tools: **Ghidra**, **x64dbg**, **x64dbg**, **Wireshark**, and **Frida**.

---

## 👤 Author & Connect

Maintained by **Rishav Anand**. Feel free to reach out for feedback, collaborations, or discussions on cybersecurity and reverse engineering.

* **LinkedIn:** [Rishav Anand](https://www.linkedin.com/in/rishav-anand-224bb5229/)
* **Medium:** [@anandrishav2228](https://medium.com/@anandrishav2228)

---

## 🤝 Contributing

Contributions are welcome! If you find any issues, want to expand a module, or add practical exercises:
1. Fork the repository.
2. Create your branch (`git checkout -b feature/update-module`).
3. Commit your updates (`git commit -m 'Add practical lab to Module 05'`).
4. Push to the branch (`git push origin feature/update-module`).
5. Open a Pull Request.

---

## 📜 License

This project is licensed under the [MIT License](LICENSE).
