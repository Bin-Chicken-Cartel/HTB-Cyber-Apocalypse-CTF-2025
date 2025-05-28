# Forensics - A New Hire

## Challenge Information
- **Category**: Forensics

## Description
This forensic challenge walks you through the analysis of an `.eml` file and navigating a hosted directory to retrieve and decode a payload that contains a flag.

## Solution

### Step 1: Download Source File
Obtain the `.eml` file provided with the challenge.

### Step 2: Open the `.eml` in Notepad

Scroll to the bottom of the file. You'll find a URL like this: ```http://94.237.51.163:42474/index.php```

### Step 3: Visit the URL and Save the PHP File

Navigate to:

👉 [http://94.237.51.163:42474/index.php](http://94.237.51.163:42474/index.php)

Save the resulting PHP page source for further inspection.

### Step 4: Open the PHP File

Open the saved PHP file in Notepad and scroll to the bottom. You'll find a link to another directory: ``` http://94.237.51.163:42474/3fe1690d955e8fd2a0b282501570e1f4/ ```

### Step 5: Explore the Directory

You can poke around the directory above or directly jump into the `configs` subdirectory:

👉 [Browse Directory](http://94.237.51.163:42474/3fe1690d955e8fd2a0b282501570e1f4/)

### Step 6: Download and Inspect `client.py`

In the `configs` folder, download or view the `client.py` file.

Inside this script, you'll find a Base64-encoded string.

### Step 7: Decode the Base64 String

Once decoded, the string reveals the flag.

## Flag
```
HTB{4PT_28_4nd_m1cr0s0ft_s34rch=1n1t14l_4cc3s!!}
```

## Notes

- The challenge demonstrates basic forensic methodology: starting from email evidence, following web trails, inspecting source code, and decoding hidden payloads.
- Tools used: Notepad, Web Browser, Base64 decoder.

Happy hacking! 🔍💻

## Write-Up Credit
**Author**: [kkc123](https://ctf.hackthebox.com/user/profile/606424)