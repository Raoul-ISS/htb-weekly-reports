# Week 01 — HTB: SpookyPass (Reversing, Very Easy)

**Student:** Raoul  
**Date:** 2025-09-09  
**Challenge:** https://app.hackthebox.com/challenges/SpookyPass  
**Category:** Reversing • **Difficulty:** Very Easy

---

## Summary
Recover the hard-coded password used by the binary and obtain the flag.  
**Flag:** `HTB{un0bfu5c4t3d_5tr1ng5}`

## Environment
- Kali Linux
- Tools: `sha256sum`, `unzip`, `file`, `checksec`, `nm`, OBS Studio

## Steps
1. **Download & verify**
   ```bash
   sha256sum SpookyPass.zip
   # Expected: 424e01ec8007fc34d29b681dcc4ad66124f48373050d262bed7a445ab578149
   unzip -P hackthebox SpookyPass.zip
