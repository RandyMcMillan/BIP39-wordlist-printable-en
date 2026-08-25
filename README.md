## BIP39-wordlist-printable-en

### A handy compact printable BIP39 mnemonic wordlist containing index values in base 10 and 11-bit binary base 2 format, based off the BIP-39 english word list found at: https://github.com/bitcoin/bips/blob/master/bip-0039/english.txt

#### **NOTE**: While valid BIP39 mnemonics recovery phrases are derived from this list programatically, that specific process involves generating cryptographically secure random numbers (initial entorpy) as well as the use of converting binary numbers to bytearrays, and applying a cryptographic hash function and thereore must rely on software code (you cannot create a valid mnemonic manually without software, see below).

 ***This word list cannot be used - on its own - to generate valid BIP39 mnemonics*** because the last word must be derived from a checksum that involves a hash function to determine the last groups of bits that must be concatenated to the end the initial string of random bits. Instead, this word list is reference for converting an existing valid mnemonic into its corresponding index values and their respective 11-bit binary numbers which can be used as an additional means of backup/recovery to reconstruct the mnemonic phrase. For those looking for an app that can generate cryptograpically-secure BIP39 Mnemonics, see the [BIP39 Mnemonic Tools] (https://github.com/hatgit/BIP39-Mnemonic-Tools) repository. 

In addition to copying the underlying text, the below images can also be printed direclty (although they are put here for descriptive purposes).

---

## How the BIP39 Checksum is Calculated

BIP39 mnemonics encode entropy plus a checksum. The checksum ensures the final word of a mnemonic is not arbitrary — it is determined by the initial entropy.

### Step-by-step

1. **Generate initial entropy** — a cryptographically secure random bit string of length `ENT` (128, 160, 192, 224, or 256 bits).

2. **Hash the entropy** — compute `SHA-256(entropy)`. Take the first `ENT/32` bits of the hash digest as the **checksum** (`CS`).

   | Entropy (ENT) | Checksum bits (CS) | Total bits (ENT+CS) | Words |
   |---|---|---|---|
   | 128 | 4 | 132 | 12 |
   | 160 | 5 | 165 | 15 |
   | 192 | 6 | 198 | 18 |
   | 224 | 7 | 231 | 21 |
   | 256 | 8 | 264 | 24 |

3. **Concatenate** — append the checksum bits to the end of the entropy bits.

4. **Split into 11-bit groups** — divide the concatenated bit string into groups of 11 bits.

5. **Look up each group** — treat each 11-bit group as a number (0–2047) and look up the corresponding word in this wordlist.

6. **The last word encodes the checksum** — the final word's 11-bit index includes the checksum bits appended in step 2, which is why the last word cannot be chosen freely.

### Example (Python)

```python
import hashlib, os, textwrap

# 1. Generate 128 bits (16 bytes) of entropy
entropy = os.urandom(16)

# 2. SHA-256 hash; take first ENT/32 = 4 checksum bits
h = hashlib.sha256(entropy).digest()
checksum_bits = format(h[0], '08b')[:4]

# 3. Concatenate entropy bits + checksum bits
entropy_bits = ''.join(format(b, '08b') for b in entropy)
full_bits = entropy_bits + checksum_bits  # 132 bits

# 4. Split into 11-bit groups
groups = textwrap.wrap(full_bits, 11)    # 12 groups

# 5. Convert each group to a wordlist index
indices = [int(g, 2) for g in groups]   # values 0-2047

# 6. Look up words using this wordlist (indices correspond to line numbers 0-2047)
print(indices)
```

### Verify a checksum (shell, using this file)

```sh
# Given a 12-word mnemonic, look up each word's index (column 1) in BIP39-en-printable.txt
# then reconstruct and verify the SHA-256 checksum of the first 128 bits.

# Example: look up the index for a word
grep -P '\tword$' BIP39-en-printable.txt
# Output: <index>\t<11-bit binary>\t<word>
```

The binary column in `BIP39-en-printable.txt` makes it straightforward to manually reconstruct the full 132-bit string from a 12-word mnemonic and verify the 4-bit checksum without software (though computing SHA-256 still requires a tool).

# Page 1: 

<img width="965" alt="bip39_printable_page_1" src="https://user-images.githubusercontent.com/5213035/46573154-a8902b80-c999-11e8-8847-c8490ce89d18.png">

# Page 2:

<img width="969" alt="bip39_printable_page_2" src="https://user-images.githubusercontent.com/5213035/46573155-aa59ef00-c999-11e8-8d81-bf598a243241.png">


