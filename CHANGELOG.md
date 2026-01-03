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

## January 4, 2026 - Session Notes

### Completed
1. **U01 Migration** - Migrated to Hyper-V, Gen 1, 16GB RAM, v200 switch
   - IP: 10.1.1.101/24, Gateway: 10.1.1.1, DNS: 8.8.8.8, 1.1.1.1
   - Applied display fix (fbdev)

2. **Fixed n8n backup on U01** - rclone gdrive token had expired
   - Re-authenticated rclone with Google Drive
   - Backup script now working: `/home/steve/backup-n8n.sh`
   - Cron: 2am daily → syncs to `gdrive:N8N`

3. **Added Playwright MCP** to Claude Code
   - Command: `claude mcp add playwright npx @playwright/mcp@latest`
   - Requires restart to activate

### Next Session
- Restart Claude Code to activate Playwright MCP
- Test Playwright browser automation
- DevVM2 RAM increased to 32GB

### SSH Access
- U01: `ssh steve@10.1.1.101` (key added to authorized_keys)
