# VM2Hyper - VMware to Hyper-V Migration

## Current Status: Migration Complete ✅

### Completed: January 3, 2026
All critical Linux VMs migrated successfully. Windows VMs skipped.

### Tools Ready
- StarWind V2V Converter: `C:\Program Files\StarWind Software\StarWind V2V Converter`
- Output folder: `V:\HV`
- Hyper-V: Enabling (reboot required)

### Quick Reference

| Item | Path |
|------|------|
| StarWind | C:\Program Files\StarWind Software\StarWind V2V Converter |
| Converted VMs | V:\HV |
| U03 source | E:\AI_LAB\U03\U03.vmx |
| U01 source | E:\AI_LAB\U01\U01.vmx |
| DevVM2 source | V:\VMs\DevVM2\DevVM2.vmx |

### Migration Status

| # | VM | Location | RAM | Status | Notes |
|---|-----|----------|-----|--------|-------|
| 1 | U03 | V:\HV | 8 GB | ✅ Done | |
| 2 | U02 | V:\HV | 8 GB | ✅ Done | |
| 3 | DevVM2 | V:\HV | 32 GB | ✅ Done | RAM upgraded |
| 4 | GUAC | V:\HV | 8 GB | ✅ Done | |
| 5 | U01 | V:\HV | 16 GB | ✅ Done | |
| 6 | R01 | E:\AI_LAB | 8 GB | ⏭️ Skipped | |
| 7 | CS01 | V:\VMs | 8 GB | ⏭️ Skipped | |
| 8 | CS02 | V:\VMs | 8 GB | ⏭️ Skipped | |
| 9 | Srv2022 | V:\VMs | 4 GB | ⏭️ Skipped | |
| 10 | SQL01 | V:\VMs | 8 GB | ⏭️ Skipped | |

### Conversion Steps (per VM)

1. Open StarWind V2V Converter
2. Source: Local file -> select `.vmdk` from VM folder
3. Destination: Local file -> Microsoft VHDX (growable)
4. Output: `V:\HV\<VM_NAME>\<VM_NAME>.vhdx`
5. Create Hyper-V VM with converted disk

### Hyper-V VM Creation (PowerShell)
```powershell
# Example for U03
New-VM -Name "U03" -MemoryStartupBytes 8GB -Generation 2 -VHDPath "V:\HV\U03\U03.vhdx" -SwitchName "Default Switch"
Set-VM -Name "U03" -ProcessorCount 8
Set-VMFirmware -VMName "U03" -EnableSecureBoot Off
```

## Documentation
- [VM_INVENTORY.md](VM_INVENTORY.md) - Full VM specs
- [MIGRATION_PLAN.md](MIGRATION_PLAN.md) - Detailed migration guide
