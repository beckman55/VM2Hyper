# VMware VM Inventory

## Summary

| Metric | Value |
|--------|-------|
| Total VMs to Migrate | 10 |
| Total vCPUs | 64 |
| Total RAM | 92 GB |
| Linux VMs | 6 |
| Windows VMs | 4 |

## High Priority VMs

| VM | OS | vCPUs | RAM | Location | Notes |
|----|-----|-------|-----|----------|-------|
| **U01** | Ubuntu 64-bit | 8 | 16 GB | E:\AI_LAB | Critical |
| **DevVM2** | Ubuntu 64-bit | 8 | 16 GB | V:\VMs | Critical |

## E:\AI_LAB (5 VMs)

| VM | OS | vCPUs | RAM | Network | NIC Type | Priority |
|----|-----|-------|-----|---------|----------|----------|
| GUAC | Ubuntu 64-bit | 8 | 8 GB | VMnet0 | e1000 | Normal |
| R01 | RHEL 10 64-bit | 8 | 8 GB | VMnet2 | vmxnet3 | Normal |
| **U01** | Ubuntu 64-bit | 8 | 16 GB | VMnet0 | e1000 | **HIGH** |
| U02 | Ubuntu 64-bit | 8 | 8 GB | VMnet0 | e1000 | Normal |
| U03 | Ubuntu 64-bit | 8 | 8 GB | VMnet0 | e1000 | Normal |

## V:\VMs (5 VMs to Keep)

| VM | OS | vCPUs | RAM | Network | NIC Type | Priority |
|----|-----|-------|-----|---------|----------|----------|
| CS01 | Windows Server 2022 | 4 | 8 GB | Default | e1000e | Normal |
| CS02 | Windows Server 2022 | 4 | 8 GB | Default | e1000e | Normal |
| **DevVM2** | Ubuntu 64-bit | 8 | 16 GB | VMnet0 | e1000 | **HIGH** |
| HA2 | Linux 2.6.x 64-bit | 4 | 4 GB | Default | e1000 | Low (test) |
| SQL01 | Windows Server 2022 | 4 | 8 GB | Default | e1000e | Normal |
| Srv2022 | Windows Server 2022 | 4 | 4 GB | Default | e1000e | Low (test) |

## VMs to Delete (Not Migrating)

| VM | OS | RAM | Location | Reason |
|----|-----|-----|----------|--------|
| ~~Fedora~~ | Fedora 64-bit | 16 GB | V:\VMs | User decision |
| ~~Fedora 2~~ | Fedora 64-bit | 16 GB | V:\VMs | User decision |
| ~~Kubuntu~~ | Ubuntu 64-bit | 8 GB | V:\VMs | User decision |
| ~~SO~~ | Ubuntu 64-bit | 16 GB | V:\VMs | User decision |
| ~~SO 2~~ | Ubuntu 64-bit | 16 GB | V:\VMs | User decision |
| ~~ESX7~~ | ESXi | - | E:\ | User decision |

**Space recovered by deletion: ~72 GB RAM worth of VMs**

## Network Configuration

| Network | VMs |
|---------|-----|
| VMnet0 | GUAC, U01, U02, U03, DevVM2 |
| VMnet2 | R01 |
| Default | CS01, CS02, HA2, SQL01, Srv2022 |

## Source Paths (VMs to Migrate)

```
E:\AI_LAB\
├── GUAC\GUAC.vmx
├── R01\R01.vmx
├── U01\U01.vmx          ← HIGH PRIORITY
├── U02\U02.vmx
└── U03\U03.vmx

V:\VMs\
├── CS01\CS01.vmx
├── CS02\CS02.vmx
├── DevVM2\DevVM2.vmx    ← HIGH PRIORITY
├── HA2\HA2.vmx
└── Was in Root\
    ├── SQL01.vmx
    └── Srv2022\Srv2022.vmx
```
