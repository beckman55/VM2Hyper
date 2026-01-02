# VMware to Hyper-V Migration Plan

## Prerequisites

### 1. Enable Hyper-V (Run as Admin in PowerShell)
```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
# Reboot required
```

### 2. Install Conversion Tools
- **StarWind V2V Converter** (Recommended, Free): https://www.starwindsoftware.com/starwind-v2v-converter
- **qemu-img** (Alternative): `winget install qemu`

### 3. Prepare Storage
Hyper-V default location: `C:\ProgramData\Microsoft\Windows\Hyper-V`
Recommended: Create dedicated folder on E: or V: drive for converted VMs

### 4. Delete Unused VMs ✅ DONE
~~Fedora, Fedora 2, Kubuntu, SO, SO 2, ESX7, HA2~~ - Deleted

## Migration Order (9 VMs)

### Phase 1: Test Migration (Low Risk)
Validate the conversion process with a non-critical VM.

| # | VM | OS | RAM | Risk | Notes |
|---|----|----|-----|------|-------|
| 1 | U03 | Ubuntu 64-bit | 8 GB | Low | Test candidate |

### Phase 2: Critical VMs (HIGH PRIORITY)
These are your most important VMs - migrate carefully after testing.

| # | VM | OS | RAM | Risk | Notes |
|---|----|----|-----|------|-------|
| 2 | **U01** | Ubuntu 64-bit | 16 GB | **HIGH** | **CRITICAL** - Full backup first |
| 3 | **DevVM2** | Ubuntu 64-bit | 16 GB | **HIGH** | **CRITICAL** - Full backup first |

### Phase 3: Lab VMs
| # | VM | OS | RAM | Risk | Notes |
|---|----|----|-----|------|-------|
| 4 | GUAC | Ubuntu 64-bit | 8 GB | Medium | Guacamole server |
| 5 | U02 | Ubuntu 64-bit | 8 GB | Medium | |
| 6 | R01 | RHEL 10 64-bit | 8 GB | Medium | Verify RHEL Hyper-V support |

### Phase 4: Windows Servers
| # | VM | OS | RAM | Risk | Notes |
|---|----|----|-----|------|-------|
| 7 | CS01 | Windows Server 2022 | 8 GB | Low | Native Hyper-V support |
| 8 | CS02 | Windows Server 2022 | 8 GB | Low | |
| 9 | Srv2022 | Windows Server 2022 | 4 GB | **MEDIUM** | ⚠️ Files moved - may need VMX repair |
| 10 | SQL01 | Windows Server 2022 | 8 GB | **HIGH** | ⚠️ Files moved - may need VMX repair |

## Conversion Process (Per VM)

### Step 1: Backup Critical VMs First
```powershell
# For U01 and DevVM2, create full backup before conversion
Copy-Item -Recurse "E:\AI_LAB\U01" "E:\AI_LAB\U01_BACKUP"
Copy-Item -Recurse "V:\VMs\DevVM2" "V:\VMs\DevVM2_BACKUP"
```

### Step 2: Shut Down VM
```
Ensure VM is powered off in VMware (not suspended)
```

### Step 3: Locate VMDK Files
```powershell
Get-ChildItem "E:\AI_LAB\<VM_NAME>" -Filter "*.vmdk"
# or
Get-ChildItem "V:\VMs\<VM_NAME>" -Filter "*.vmdk"
```

### Step 4: Convert VMDK to VHDX

#### Option A: StarWind V2V (GUI - Recommended)
1. Open StarWind V2V Converter
2. Select source: VMware VMDK
3. Select destination: Microsoft VHDX (growable)
4. Choose output location
5. Convert

#### Option B: qemu-img (CLI)
```powershell
qemu-img convert -f vmdk -O vhdx "source.vmdk" "destination.vhdx"
```

### Step 5: Create Hyper-V VM
```powershell
# Example for U01 (Ubuntu, 16GB RAM, 8 vCPUs)
New-VM -Name "U01" `
    -MemoryStartupBytes 16GB `
    -Generation 2 `
    -VHDPath "E:\Hyper-V\U01\U01.vhdx" `
    -SwitchName "Default Switch"

Set-VM -Name "U01" -ProcessorCount 8
Set-VMFirmware -VMName "U01" -EnableSecureBoot Off  # Required for Linux
```

### Step 6: Configure Network
```powershell
# Create virtual switches to match VMware networks
New-VMSwitch -Name "VMnet0" -SwitchType Internal
New-VMSwitch -Name "VMnet2" -SwitchType Internal

# Attach VM to appropriate switch
Connect-VMNetworkAdapter -VMName "U01" -SwitchName "VMnet0"
```

### Step 7: Boot and Verify
1. Start VM in Hyper-V Manager
2. Verify OS boots correctly
3. Check network connectivity
4. Install Hyper-V Integration Services (if needed)

## Network Mapping

| VMware Network | Hyper-V Equivalent | VMs |
|----------------|-------------------|-----|
| VMnet0 | Internal Switch "VMnet0" | GUAC, U01, U02, U03, DevVM2 |
| VMnet2 | Internal Switch "VMnet2" | R01 |
| Default/Bridged | Default Switch | CS01, CS02, HA2, SQL01, Srv2022 |

## VM-Specific Commands

### U01 (Critical)
```powershell
New-VM -Name "U01" -MemoryStartupBytes 16GB -Generation 2 -VHDPath "E:\Hyper-V\U01\U01.vhdx"
Set-VM -Name "U01" -ProcessorCount 8
Set-VMFirmware -VMName "U01" -EnableSecureBoot Off
Connect-VMNetworkAdapter -VMName "U01" -SwitchName "VMnet0"
```

### DevVM2 (Critical)
```powershell
New-VM -Name "DevVM2" -MemoryStartupBytes 16GB -Generation 2 -VHDPath "V:\Hyper-V\DevVM2\DevVM2.vhdx"
Set-VM -Name "DevVM2" -ProcessorCount 8
Set-VMFirmware -VMName "DevVM2" -EnableSecureBoot Off
Connect-VMNetworkAdapter -VMName "DevVM2" -SwitchName "VMnet0"
```

## Post-Migration Checklist

### Per-VM Checklist
- [ ] VM boots successfully in Hyper-V
- [ ] Network connectivity verified
- [ ] Applications function correctly
- [ ] Remove VMware Tools (if installed)
- [ ] Install Hyper-V Integration Services

### Final Checklist
- [ ] All 10 VMs migrated and tested
- [ ] Backups configured in Hyper-V
- [ ] Delete VMware backup copies (after verification)
- [ ] VMware Workstation uninstalled (optional)
- [ ] Reclaim disk space from old VMDK files

## Rollback Plan

Keep original VMware VMs until migration is verified:
1. Do NOT delete source VMDK files until VM is tested
2. Keep VMware Workstation installed during transition
3. Test each VM thoroughly before marking complete
4. For U01 and DevVM2 - keep backups for 30 days

## Estimated Storage Requirements

| VM | RAM | Est. VHDX Size |
|----|-----|---------------|
| HA2 | 4 GB | 20-30 GB |
| Srv2022 | 4 GB | 40-50 GB |
| U01 | 16 GB | 50-80 GB |
| DevVM2 | 16 GB | 50-80 GB |
| GUAC | 8 GB | 30-50 GB |
| U02 | 8 GB | 30-50 GB |
| U03 | 8 GB | 30-50 GB |
| R01 | 8 GB | 30-50 GB |
| CS01 | 8 GB | 40-60 GB |
| CS02 | 8 GB | 40-60 GB |
| SQL01 | 8 GB | 50-80 GB |
| **Total** | **92 GB** | **400-600 GB** |

## Known Issues

### Linux VMs (U01, DevVM2, GUAC, U02, U03)
- Secure Boot must be disabled (Generation 2)
- May need to regenerate initramfs for Hyper-V drivers
- If boot fails, try Generation 1 VM

### RHEL (R01)
- Verify RHEL 10 Hyper-V support
- Install hyper-v packages: `dnf install hyperv-daemons`

### Windows VMs (CS01, CS02, SQL01, Srv2022)
- Should migrate smoothly (native Hyper-V support)
- May need to reactivate Windows
- Remove VMware Tools after migration
