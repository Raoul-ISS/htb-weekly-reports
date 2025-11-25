# Week 07 – picoCTF: Hurry up! Wait! (Reversing, Hard)

**Student:** Raoul  
**Date:** 2025-11-25  
**Challenge:** https://play.picoctf.org/practice/challenge/165  
**Category:** Reverse Engineering • **Difficulty:** Hard  

---

## Summary

In *Hurry up! Wait!* I reversed a 64-bit ELF PIE binary that prints a hidden picoCTF flag.  
Instead of manually stepping through dozens of tiny helper functions, I used Ghidra’s scripting engine to automatically recover each character from `.rodata` and reconstruct the flag.

---

## Tools Used

- **Kali Linux** VM  
- **Ghidra** (CodeBrowser + Script Manager)  
- **Jython (Python scripting in Ghidra)**  
- Linux terminal tools: `mkdir`, `file`  

---

## Environment Setup

```bash
mkdir -p ~/Desktop/CTF/Hurry_up_Wait
cd ~/Desktop/CTF/Hurry_up_Wait
# svchost.exe downloaded from picoCTF challenge page into this directory
file svchost.exe

## Methodology
Created a working directory and downloaded `svchost.exe`.
Loaded the binary in Ghidra and enabled auto-analysis.
Identified the main dispatcher function at `FUN_0010298a`.
Observed multiple calls to `FUN_00xxxx` helper functions.
Wrote a Ghidra script to:
   - Locate each helper function
   - Extract the byte loaded by the LEA instruction
   - Convert each byte into the final flag string
Ran the script and obtained the correct flag.(picoCTF{d15a5m_ftw_dfbdc5d})
