# How to Change the Admin Panel Password on DL5M2 and Other OpenWRT Routers

> **Disclaimer:**  
> Incorrect changes may lock you out of the web UI. Always create backups before editing.

---

## Requirements
- USB ↔ UART adapter soldered to the router’s UART pins.  
  Use **1.8V** level and **921600 baud** connection.  
- Terminal program (e.g. `screen`, `minicom`, `picocom`, `putty`).
- Preferred MobaXterm   
- Password hash generator: [https://unix4lyfe.org/crypt/](https://unix4lyfe.org/crypt/).  

---

## Steps

### 1. Connect via UART

![IMG_0122](https://github.com/user-attachments/assets/61e7c204-f532-44ea-897e-88e2980f83c1)
![IMG_0123](https://github.com/user-attachments/assets/c90e4ee1-4099-4294-aa4f-b90905519bc4)
![IMG_0124](https://github.com/user-attachments/assets/151702c4-a513-4271-8967-e5f1d30bbd98)

Connect the USB-UART adapter to the router pins (1.8V, 921600 baud) and open a terminal, for example:
```bash
screen /dev/ttyUSB0 921600
```

---

### 2. Reduce Kernel Log Spam (Optional)
Run:
```bash
dmesg -n 1
```

---

### 3. Backup the Config File
Before making changes, create a backup:
```bash
cp /etc/config/webui.cfg /etc/config/webui.cfg.bak
```

---

### 4. Generate a New Password Hash
1. Open [https://unix4lyfe.org/crypt/](https://unix4lyfe.org/crypt/).  
2. Choose **MD5 crypt** (hashes starting with `$1$`).  
3. Use the same **SALT** as in your current config (e.g. `jd77a//t`).  
4. Copy the generated hash, e.g.:  
   ```
   $1$jd77a//t$aDTQ1LFL34NWiGNNUP4Es/
   ```

---

### 5. Edit `/etc/config/webui.cfg`
Open the file:
```bash
vi /etc/config/webui.cfg
```

Inside `vi`:  
- Press `i` to enter insert mode.  
- Replace the password line with the new hash, for example:
  ```conf
  PASSWORD_CRYPT = "$1$jd77a//t$aDTQ1LFL34NWiGNNUP4Es/";
  ```
- Update flags:
  ```conf
  FIRST_LOGIN = 0;
  failed_access_attempts = 0;
  ```
- Save and exit: press `Esc`, type `:wq`, press `Enter`.

---

### 6. Edit `/etc/sofa_default_cfg/webui.cfg` (If Present)
Do the same edits in this file:
```bash
vi /etc/sofa_default_cfg/webui.cfg
```

---

### 7. Reboot the Router
```bash
reboot
```

After reboot, log in with your new password.

---

## Restore from Backup
If something goes wrong:
```bash
cp /etc/config/webui.cfg.bak /etc/config/webui.cfg
reboot
```

---

## Quick Command Summary
```bash
# Optional: reduce kernel logging
dmesg -n 1

# Backup config
cp /etc/config/webui.cfg /etc/config/webui.cfg.bak

# Edit config
vi /etc/config/webui.cfg
# → Set PASSWORD_CRYPT = "<YOUR_HASH>";
# → Set FIRST_LOGIN = 0;
# → Set failed_access_attempts = 0;

# Repeat for sofa_default_cfg if exists
vi /etc/sofa_default_cfg/webui.cfg

# Reboot
reboot
```
