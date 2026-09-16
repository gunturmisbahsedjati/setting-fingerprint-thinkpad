### WORKING Setup for Synaptics Metallica MIS (06cb:009a) on **KDE NEON 2026 / Ubuntu 24.04 (noble)**

> Based on https://github.com/uunicorn/python-validity/issues/202#issuecomment-2760608822

---

#### 💻 Setup:
- Lenovo Thinkpad A485
- KDE neon User Edition 2026
- Kernel 7.0.0-31-generic
- Fingerprint : Metallica MIS Touch Fingerprint Reader (06cb:009a Synaptics, Inc.)

#### 🛠️ Steps:

```bash
# 1. Add the PPA
sudo add-apt-repository ppa:uunicorn/open-fprintd

# 2. Update + fix conflicting packages
sudo apt update

# if fprintd is already installed. If not, proceed directly to step 3.
sudo apt remove fprintd

# 3. Install everything you need
sudo apt install open-fprintd fprintd-clients python3-validity

# 4. Enroll a fingerprint (test if it's working)
fprintd-enroll

# 5. Verify whether the fingerprint scanner is working or not.
fprintd-verify

# 6. Enable PAM auth
sudo pam-auth-update
# choose Fingerprint authentication and OK

# 7. Enable suspend/resume helpers
sudo systemctl enable open-fprintd-resume open-fprintd-suspend
```

---

### 🎉🎉🎉 Result:

- Working fingerprint login
- Fully integrated into KDE NEON 2026 login & sudo prompts
- Reader: **Synaptics 06cb:009a**
- System: **KDE NEON 2026** / **Ubuntu 24.04 (noble)** base  
- Kernel: `7.0.0`

---
### BONUS

- To extend timeout
```bash
# to extend the timeout duration
sudo nano /etc/fprintd.conf
# change to
[storage]
type=file

[daemon]
timeout=60

sudo systemctl restart open-fprintd python3-validity
```

- Auto-Restart systemd rule (if lockscreen timeout not showing verification fingerprint)
```bash
sudo systemctl edit python3-validity
# add lines
[Service]
Restart=on-failure
RestartSec=1s

sudo mkdir -p /etc/systemd/system/python3-validity.service.d/
echo -e "[Service]\nRestart=on-failure\nRestartSec=1s" | sudo tee /etc/systemd/system/python3-validity.service.d/override.conf


sudo systemctl edit open-fprintd
# add lines
[Service]
Restart=on-failure
RestartSec=1s

sudo mkdir -p /etc/systemd/system/open-fprintd.service.d/
echo -e "[Service]\nRestart=on-failure\nRestartSec=1s" | sudo tee /etc/systemd/system/open-fprintd.service.d/override.conf


sudo systemctl daemon-reload
sudo systemctl restart open-fprintd python3-validity
```

> Hoping to help fellow Lenovo ThinkPad A485 users who are confused.❤️