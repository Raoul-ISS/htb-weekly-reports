# Week 09 – picoCTF: gogo (Reversing, Hard)

**Student:** Raoul  
**Date:** 2025-11-26  
**Challenge:** https://play.picoctf.org/practice/challenge/171  
**Category:** Reverse Engineering • **Difficulty:** Hard  

## Summary

In *gogo* I reversed a 32-bit ELF binary that validates a secret password and then talks to a remote service. Using Ghidra and gdb I recovered the two constant byte arrays used in the check and observed that the program verifies each character as `input[i] ^ key[i] == expected[i]`. By XORing these arrays in Python I obtained the cleartext password, used it to authenticate to `mercury.picoctf.net:48728`, reversed an MD5 hash to find the unhashed key, and finally retrieved the picoCTF flag.

## Environment

- Kali Linux VM  
- Ghidra, gdb, Python 3  
- `nc` (netcat)  
- Firefox + MD5 reverse website  

## Steps

1. **Download & triage**  
   - Saved `enter_password` from picoCTF to `~/Desktop/CTF/gogo`.  
   - Verified it is a 32-bit LSB executable and observed the password prompt.

2. **Static analysis**  
   - Imported the binary into a Ghidra project named `gogo`.  
   - Located `main.checkPassword` and identified the XOR-based check between `input`, `key[]` and `expected[]`.

3. **Dynamic analysis**  
   - Set a breakpoint in `main.checkPassword` with gdb and ran the program.  
   - Dumped 32-byte `key` and `expected` arrays from the stack with `x/32xb`.

4. **Password recovery**  
   - Wrote a short Python snippet to compute `password[i] = key[i] ^ expected[i]`.  
   - Recovered the valid password string.

5. **Remote interaction**  
   - Connected to `mercury.picoctf.net 48728` with `nc` and sent the password.  
   - Received an MD5 hash and used an online lookup to obtain the unhashed key.

6. **Flag**  
   - Re-connected, sent the password and unhashed key, and obtained the flag:

   ```text
   picoCTF{p1kap1ka_p1c0b187f1db}
