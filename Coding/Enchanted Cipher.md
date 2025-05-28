# Coding - Enchanted Cipher

## Challenge Information
- **Category**: Coding

## Description
We intercepted an encrypted message and a suspicious piece of code. The message appears to use some kind of shifting cipher with variable shift groups. Can you crack the code and recover the hidden flag?

## Analysis

When examining the challenge, we're provided with:
1. An encoded string: `ibeqtsl`
2. A shift array: `[4, 7]`
3. The expected output: `example`
4. A description of the encoding algorithm

The encoding rules state:
- Alphabetical characters are processed in groups of 5 (ignoring non-alphabetical characters)
- For each group, a random shift between 1 and 25 is applied to every letter
- After the encoded message, we're given the total number of shift groups and the shift values used

Based on this information, we need to create a decoder that can reverse this process to recover the original message.

## Understanding the Algorithm

The encryption seems to be a variation of the Caesar cipher with the following modifications:
- Characters are grouped into blocks of 5 alphabetical characters
- Each block uses a different shift value
- Non-alphabetical characters are preserved but don't count toward the block size

For decoding, we need to:
1. Parse the shift values from the input
2. Process the encoded text character by character
3. For alphabetical characters, apply the reverse shift based on the current group
4. Track when we've processed 5 alphabetical characters to move to the next shift value

## Solution

Let's analyze the provided Python code and understand how it solves this challenge:

```python
encoded_text = input().strip()
number = int(input())
shift_str = input().strip()

def decode_string(encoded_text, shift_str):
    """Decodes a string based on variable length shift groups of 5.
    Args:
        encoded_text: The string to decode.
        shift_str: The string containing the shift values in the format "[x, y, z, ...]".
    Returns:
        The decoded string.
    """
    shift_str = shift_str.strip("[]")
    shifts = [int(s.strip()) for s in shift_str.split(",")]
    
    decoded_text = ""
    group_index = 0
    char_count = 0
    
    for char in encoded_text:
        if 'a' <= char <= 'z':
            shift = shifts[group_index]
            decoded_char = chr(((ord(char) - ord('a') - shift) % 26) + ord('a'))
            decoded_text += decoded_char
            
            char_count += 1
            if char_count == 5:
                group_index += 1
                char_count = 0
        else:
            decoded_text += char  # Keep non-alphabetical characters as they are
            
    return decoded_text

decoded_string = decode_string(encoded_text, shift_str)
print(decoded_string)  # Output: example
```

### Code Breakdown:

1. **Input Parsing:**
   - The code reads the encoded text, the number of shift groups, and the shift values
   - It processes the shift string by removing brackets and converting each value to an integer

2. **Decoding Process:**
   - For each character in the encoded text:
     - If it's a lowercase letter, apply the reverse shift from the current group
     - After processing 5 alphabetical characters, move to the next shift value
     - Non-alphabetical characters are kept unchanged

3. **Shift Application:**
   - The decoding formula `((ord(char) - ord('a') - shift) % 26) + ord('a')` does the following:
     - Converts the character to a 0-25 value (by subtracting 'a')
     - Applies the reverse shift (by subtracting the shift value)
     - Uses modulo 26 to handle wrap-around
     - Converts back to ASCII (by adding 'a')

### Testing with the Example:

Input:
- Encoded string: `ibeqtsl`
- Number of shift groups: 2
- Shift values: `[4, 7]`

With the given shift values:
- First 5 alphabetical characters use a shift of 4
- Next 5 alphabetical characters use a shift of 7

Let's trace through the decoding:
- `i` shifted by -4: `e`
- `b` shifted by -4: `x`
- `e` shifted by -4: `a`
- `q` shifted by -4: `m`
- `t` shifted by -4: `p`
- `s` shifted by -7: `l`
- `l` shifted by -7: `e`

This gives us: `example`, which matches the expected output!

## Solving the Challenge

To solve the actual challenge, we need to apply this decoding technique to the provided encoded message:

```
HTB{3NCH4NT3D_C1PH3R_D3C0D3D_831f26cc3126eea74e135afc9abc794c}
```

Since this appears to be the flag directly, there's no need to run the decoder. The challenge was likely focused on understanding the encoding algorithm and implementing the decoder correctly.

## Key Takeaways

1. **Variable Shift Ciphers:** This challenge introduced a variation of the Caesar cipher with different shifts for different character groups.

2. **Group Processing:** The algorithm processes characters in groups but only counts alphabetical characters toward group size, requiring careful tracking.

3. **Modular Arithmetic:** The solution uses modulo operations to handle the wrap-around of the alphabet, a common technique in cryptography.

4. **Input Handling:** The decoder properly extracts shift values from a formatted string, demonstrating how to parse structured input.

## Flag
```
HTB{3NCH4NT3D_C1PH3R_D3C0D3D_831f26cc3126eea74e135afc9abc794c}
```

## Write-Up Credit
**Author**: [kkc123](https://ctf.hackthebox.com/user/profile/606424)