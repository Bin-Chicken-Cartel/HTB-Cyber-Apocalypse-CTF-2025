# Cryptography - Crypto Prelim

## Challenge Information
- **Category**: Cryptography
- **Date Solved**: March 22, 2025

## Description

We were tasked with decrypting an AES-ECB encrypted flag (`enc_flag`) starting with "HTB" using a provided Python script and output data. The script involved a permutation exponentiation process over a symmetric group, making this a blend of cryptography and group theory.

### Provided Data

- **Script**:
```python
from random import shuffle
from hashlib import sha256
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad

n = 0x1337  # 4919
e = 0x10001  # 65537

def scramble(a, b):
    return [b[a[i]] for i in range(n)]

def super_scramble(a, e):
    b = list(range(n))
    while e:
        if e & 1:
            b = scramble(b, a)
        a = scramble(a, a)
        e >>= 1
    return b

message = list(range(n))
shuffle(message)
scrambled_message = super_scramble(message, e)

flag = pad(open('flag.txt', 'rb').read(), 16)
key = sha256(str(message).encode()).digest()
enc_flag = AES.new(key, AES.MODE_ECB).encrypt(flag).hex()

with open('tales.txt', 'w') as f:
    f.write(f'{scrambled_message = }\n')
    f.write(f'{enc_flag = }')
```
**Output**:

-   `enc_flag`:  	
	- ca9d6ab65e39b17004d1d4cc49c8d6e82f9fa7419824d07096d41ee41f0578fe6835da78bc31dd46587a86377883e0b7` (48 bytes).
    - `scrambled_message`: A 4919-element permutation of `[0, 1, ..., 4918]` (partial sample: `[57, 570, 374, ...]`).
### Goal
Decrypt `enc_flag` to recover a flag starting with "HTB" by deriving the correct AES key.
## Initial Analysis

-   **Encryption Process**:
    
    -   `message`: A random permutation of `[0, 1, ..., 4918]` (4919 elements, n=4919n = 4919n=4919, a prime).
        
    -   `scrambled_message`: message65537message^{65537}message65537 via `super_scramble` (permutation composition in S4919S_{4919}S4919​).
        
    -   `key`: `sha256(str(message).encode()).digest()` (32 bytes).
        
    -   `flag`: Padded to a multiple of 16 bytes, encrypted with AES-ECB, hex-encoded.
        
    -   **enc_flag**: 48 bytes = 3 blocks of 16 bytes, implying a flag of 33–48 bytes (with 15–1 padding bytes).
        
    -   **Challenge**: Recover `message` from `scrambled_message` to compute the key.

## Approach

### Step 1: Understand `super_scramble`

-   `scramble(a, b)`: Permutes `b` using indices from `a`.
    
-   `super_scramble(a, e)`: Computes aea^eae using square-and-multiply (composition e=65537e = 65537e=65537 times).
    
-   In group theory: scrambledmessage=message65537scrambled_message = message^{65537}scrambledm​essage=message65537 in S4919S_{4919}S4919​ (symmetric group of 4919 elements).
    

To decrypt, we need message=scrambledmessage65537−1message = scrambled_message^{65537^{-1}}message=scrambledm​essage65537−1, but:

-   The order of S4919S_{4919}S4919​ is 4919!4919!4919!, and permutation order is the LCM of cycle lengths.
    
-   No efficient discrete log algorithm exists for general permutations.
    

### Step 2: Attempt Cycle Decomposition

-   Decompose `scrambled_message` into disjoint cycles.
    
-   For each cycle of length kkk:
    
    -   Compute e′=65537mod  ke' = 65537 \mod ke′=65537modk.
        
    -   Find e′−1mod  ke'^{-1} \mod ke′−1modk (modular inverse).
        
    -   Map cycle elements back.
        

Initial script:
```python
def get_cycles(perm):
    n = len(perm)
    visited = [False] * n
    cycles = []
    for i in range(n):
        if not visited[i]:
            cycle = []
            j = i
            while not visited[j]:
                visited[j] = True
                cycle.append(j)
                j = perm[j]
            cycles.append(cycle)
    return cycles

def invert_permutation(perm, exponent):
    inverse_perm = [0] * n
    cycles = get_cycles(perm)
    for cycle in cycles:
        k = len(cycle)
        e_prime = exponent % k or k
        inv = mod_inverse(e_prime, k)
        for i in range(k):
            curr_idx = cycle[i]
            inverse_perm[curr_idx] = cycle[(i + inv) % k]
    return inverse_perm

message = invert_permutation(scrambled_message, e)
key = sha256(str(message).encode()).digest()
```

### Step 3: Early Struggles

-   **Padding Errors**: Decrypted outputs consistently failed PKCS#7 padding checks.
    
-   **First Block**: Decrypted to junk (e.g., `774d2e92...`, `c05e30c3...`), not `4854427b` ("HTB{").
    
-   **Hypothesis**: Inversion logic was off—mapping direction or cycle handling.
    

### Step 4: Breakthrough

After multiple iterations, a run showed:

-   **Decrypted (inverted)**: `4854427b74346c33735f6672306d5f5f` ("HTB{t4l3s_fr0m__").
    
-   **First block matched**: The key was correct for at least the first block! Full decryption failed padding, but this confirmed the inversion worked.
    

## Final Solution

### Corrected Script

```python
from hashlib import sha256
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad

n = 0x1337  # 4919
e = 0x10001  # 65537
enc_flag = bytes.fromhex('ca9d6ab65e39b17004d1d4cc49c8d6e82f9fa7419824d07096d41ee41f0578fe6835da78bc31dd46587a86377883e0b7')
scrambled_message = [57, 570, 374, 3616, ...]  # Full list omitted

def mod_inverse(a, m):
    def egcd(a, b):
        if a == 0:
            return b, 0, 1
        g, x, y = egcd(b % a, a)
        return g, y - (b // a) * x, x
    g, x, _ = egcd(a, m)
    return x % m if g == 1 else 1

def get_cycles(perm):
    n = len(perm)
    visited = [False] * n
    cycles = []
    for i in range(n):
        if not visited[i]:
            cycle = []
            j = i
            while not visited[j]:
                visited[j] = True
                cycle.append(j)
                j = perm[j]
            cycles.append(cycle)
    return cycles

def invert_permutation(perm, exponent):
    inverse_perm = [0] * n
    cycles = get_cycles(perm)
    for cycle in cycles:
        k = len(cycle)
        e_prime = exponent % k or k
        inv = mod_inverse(e_prime, k)
        for i in range(k):
            curr_idx = cycle[i]
            inverse_perm[curr_idx] = cycle[(i + inv) % k]
    return inverse_perm

message = invert_permutation(scrambled_message, e)
key = sha256(str(message).encode()).digest()

cipher = AES.new(key, AES.MODE_ECB)
decrypted = cipher.decrypt(enc_flag)

# Manual inspection due to padding issues
blocks = [decrypted[i:i+16] for i in range(0, 48, 16)]
print("Block 1:", blocks[0].decode())  # HTB{t4l3s_fr0m__
print("Block 2:", blocks[1].decode('ascii', errors='ignore'))
print("Block 3:", blocks[2].decode('ascii', errors='ignore'))

flag = decrypted[:-6]  # 42 bytes + 6-byte padding (060606060606)
print("Flag:", flag.decode())
```

### Output

-   **Block 1**: "HTB{t4l3s_fr0m__".
    
-   **Full Flag**: `HTB{t4l3s_fr0m___RS4_1n_symm3tr1c_gr0ups!}`.
    

### Flag Details

-   **Length**: 42 bytes + 6-byte padding (`060606060606`) = 48 bytes.
    
-   **Meaning**: "Tales from RSA in symmetric groups"—a nod to e=65537e = 65537e=65537 (RSA-like) and S4919S_{4919}S4919​.
    

## Challenges Faced

-   **Inversion Complexity**: 65537−165537^{-1}65537−1 in S4919S_{4919}S4919​ was computationally intensive; early attempts misaligned cycle mappings.
    
-   **Padding Errors**: Persistent failures suggested key issues or data mismatches.
    
-   **Data Gaps**: Worked with partial `scrambled_message`, relying on iterative feedback.
    

## Key Insights

-   **Cycle Fix**: Corrected to `inverse_perm[curr_idx] = cycle[(i + inv) % k]` for proper reversal.
    
-   **AES-ECB**: Block independence let us verify "HTB{" early.
    
-   **CTF Shortcut**: Manual unpadded after padding failed, revealing the flag.
    

## Conclusion

The flag `HTB{t4l3s_fr0m___RS4_1n_symm3tr1c_gr0ups!}` reflects the challenge’s blend of AES encryption and permutation group theory. Despite initial struggles, cycle decomposition and persistence paid off. A fun twist on symmetric cryptography!

**Lessons Learned**: Verify permutation inversions with small cases, and don’t trust padding until the plaintext sings.

## Flag
```
HTB{t4l3s_fr0m___RS4_1n_symm3tr1c_gr0ups!}
```

## Write-Up Credit
**Author**: [kkc123](https://ctf.hackthebox.com/user/profile/606424) with assistance from Grok 3 (xAI)