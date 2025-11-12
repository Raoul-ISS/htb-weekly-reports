# Week 04 – HTB: Rega's Town (Reversing, Medium)

**Student:** Raoul  
**Date:** 2025-11-11  
**Challenge:** https://app.hackthebox.com/challenges/Rega-s-Town  
**Category:** Reversing • **Difficulty:** Medium

## Summary

In Rega's Town I reversed a Rust binary, `rega_town`, that asks for a secret passphrase. By extracting regular expressions and product-based constraints from the binary, I wrote a Python solver to find all valid segments and recovered the flag `HTB{Y0u_Ar3_Th3_K1ng_07_ThE_Town}`, which the binary and Hack The Box both accepted.

## Setup & Environment

- Kali Linux 2025.3 VM  
- ZIP archive downloaded from the HTB challenge page  
- Challenge files extracted into `~/Downloads/dist`

cd ~/Downloads
unzip Rega\ s\ Town.zip   # password: hackthebox
cd dist
chmod +x rega_town
Recon & Static Analysis
I first ran the binary and then inspected it with file, radare2, and objdump to understand how the passphrase is checked.

./rega_town
# "Welcome to our secret town!"

file rega_town
# 64-bit PIE ELF (Rust), not stripped

r2 rega_town
aaa
izz | grep -A1 -B1 "Maybe next time"
q

objdump -r rega_town | grep "mul\|product"
From the strings and symbols I recovered the regex pattern for each segment of the passphrase and the integer “products” used to validate them.

Python Solver
I then reimplemented the check logic in Python using the recovered products and regexes and brute-forced each small segment.

nano rega_town.py
# (see repo for full script)

python3 rega_town.py
# -> HTB{Y0u_Ar3_Th3_K1ng_O7_The_Town}
Flag Verification
Finally, I verified the recovered flag directly in the binary and on Hack The Box.

./rega_town
Enter secret passphrase:
HTB{Y0u_Ar3_Th3_K1ng_O7_The_Town}
# -> "Correct one of us!!"
Final flag: HTB{Y0u_Ar3_Th3_K1ng_O7_The_Town}
