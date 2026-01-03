# Changelog
All notable changes to this project will be documented here.

## January 3, 2026 - Migration Complete

### VMs Migrated to Hyper-V
- **U03** - 8 GB RAM, Generation 1
- **U02** - 8 GB RAM, Generation 1
- **DevVM2** - 32 GB RAM (upgraded from 16 GB), Generation 1
- **GUAC** - 8 GB RAM, Generation 1
- **U01** - 16 GB RAM, Generation 1

### Key Findings
- All Ubuntu VMs required **Generation 1** (BIOS/MBR disks don't boot in Gen 2)
- Network interface changes from `ens33` to `eth0` after migration
- Display fix required for GUI: `/etc/X11/xorg.conf.d/10-hyperv.conf` with fbdev driver
- All VMs on `v200` switch (10.1.1.0/24 network)

### Hyper-V Display Fix (Ubuntu)
```bash
sudo tee /etc/X11/xorg.conf.d/10-hyperv.conf << 'EOF'
Section "Device"
    Identifier  "Card0"
    Driver      "fbdev"
    Option      "fbdev" "/dev/fb0"
EndSection
EOF
sudo systemctl restart lightdm
```

### VMs Skipped
- R01, CS01, CS02, Srv2022, SQL01 (Windows servers and RHEL)
