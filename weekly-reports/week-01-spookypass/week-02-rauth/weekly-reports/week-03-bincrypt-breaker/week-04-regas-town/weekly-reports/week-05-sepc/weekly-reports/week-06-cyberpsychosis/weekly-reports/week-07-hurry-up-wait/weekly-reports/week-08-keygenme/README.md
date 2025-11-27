# Week 08 – picoCTF: Keygenme (Reversing, Hard)

**Student:** Raoul  
**Date:** 2025-11-26  
**Challenge:** https://play.picoctf.org/practice/challenge/276  
**Category:** Reverse Engineering • **Difficulty:** Hard

---

## Summary

In **Keygenme** I reversed a 64-bit ELF PIE binary that validates a “license key” and hides the picoCTF flag inside its key-generation logic.  
Using Ghidra and a small Python script, I recovered the exact algorithm the binary uses (two MD5 hashes and a fixed prefix) and rebuilt the correct flag offline.

---

## Environment

- **OS:** Kali Linux 2025.3 (VM)
- **Tools:**
  - `file`, `chmod` (basic ELF inspection)
  - **Ghidra** (static analysis / decompilation)
  - **Python 3** (re-implementing the keygen logic)
  - Mousepad (editing `solve_keygenme.py`)

---

## Binary Recon

Working directory:

```bash
mkdir -p ~/Desktop/CTF/keygenme
mv ~/Downloads/keygenme ~/Desktop/CTF/keygenme/
cd ~/Desktop/CTF/keygenme
chmod +x keygenme
file keygenme

Result: the file is a 64-bit LSB pie executable, dynamically linked and stripped, so there are no symbols to help us.

In the decompiler I found this key logic (simplified):

builtin_memcpy(local_98, "picoCTF{bring_y0ur_0wn_k3y_", 0x1c);
local_b0[0] = '}';
sVar1 = strlen((char *)local_98);
MD5(local_98, local_b8);
sVar1 = strlen((char *)local_ba);
MD5(local_ba, local_a8);

/* later: hex-encode local_b8 and local_a8 and print them */

I created solve_keygenme.py:

import hashlib
prefix = b"picoCTF{bring_y0ur_0wn_k3y_"
suffix1 = hashlib.md5(prefix).hexdigest()
suffix2 = hashlib.md5(b"}").hexdigest()
flag = prefix.decode() + suffix1 + suffix2 + "}"
print(flag)

Run: python3 solve_keygenme.py

Output: picoCTF{bring_y0ur_0wn_k3y_7b692422d79c272639823d732007e743cbb184dd8e05c9709e5dcaedaa0495cf}

Submitting this string as the license key on PicoCTF solved the challenge.

Final Flag: picoCTF{bring_y0ur_0wn_k3y_7b692422d79c272639823d732007e743cbb184dd8e05c9709e5dcaedaa0495cf}
