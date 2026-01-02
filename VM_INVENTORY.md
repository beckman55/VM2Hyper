# VMware VM Inventory

## Summary

| Metric | Value |
|--------|-------|
| Total VMs to Migrate | 9 |
| Total vCPUs | 60 |
| Total RAM | 88 GB |
| Linux VMs | 5 |
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

## V:\VMs (4 VMs to Keep)

| VM | OS | vCPUs | RAM | Network | NIC Type | Priority | Notes |
|----|-----|-------|-----|---------|----------|----------|-------|
| CS01 | Windows Server 2022 | 4 | 8 GB | Default | e1000e | Normal | |
| CS02 | Windows Server 2022 | 4 | 8 GB | Default | e1000e | Normal | |
| **DevVM2** | Ubuntu 64-bit | 8 | 16 GB | VMnet0 | e1000 | **HIGH** | |
| SQL01 | Windows Server 2022 | 4 | 8 GB | Default | e1000e | Normal | ⚠️ Files moved |
| Srv2022 | Windows Server 2022 | 4 | 4 GB | Default | e1000e | Normal | ⚠️ Files moved |

## VMs Deleted ✅

~~Fedora, Fedora 2, Kubuntu, SO, SO 2, ESX7, HA2~~ - All deleted

## Network Configuration

| Network | VMs |
|---------|-----|
| VMnet0 | GUAC, U01, U02, U03, DevVM2 |
| VMnet2 | R01 |
| Default | CS01, CS02, SQL01, Srv2022 |

## Source Paths (VMs to Migrate)

```
E:\AI_LAB\
├── GUAC\GUAC.vmx
├── R01\R01.vmx
├── U01\U01.vmx          ← HIGH PRIORITY
├── U02\U02.vmx
└── U03\U03.vmx          ← TEST FIRST

V:\VMs\
├── CS01\CS01.vmx
├── CS02\CS02.vmx
├── DevVM2\DevVM2.vmx    ← HIGH PRIORITY
└── Was in Root\
    ├── SQL01.vmx        ⚠️ Files moved
    └── Srv2022\Srv2022.vmx  ⚠️ Files moved
```
