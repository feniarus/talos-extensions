# NVIDIA DOCA OFED Extension

This extension provides MLNX_OFED drivers for NVIDIA BlueField DPUs and ConnectX NICs.

## Profile: doca-ofed

This is the minimal driver-only installation, equivalent to MLNX_OFED without additional DOCA functionality.

## What's Included

- **Kernel Modules:**
  - mlx5_core - Core driver for ConnectX and BlueField devices
  - mlx5_ib - InfiniBand support
  - ib_core, ib_uverbs - RDMA/InfiniBand core
  - rdma_cm - RDMA connection manager
  - ib_ipoib - IP over InfiniBand

- **Userspace Libraries:**
  - rdma-core - RDMA userspace libraries

## Supported Hardware

- **BlueField DPUs:** BlueField-3, BlueField-2
- **ConnectX NICs:** ConnectX-8, ConnectX-7, ConnectX-6 DX/LX, ConnectX-5, ConnectX-4 LX

## Installation

```yaml
machine:
  install:
    extensions:
      - image: ghcr.io/siderolabs/nvidia-doca-ofed:VERSION
```

## When to Use This Extension

Use `nvidia-doca-ofed` when you:
- Need basic MLNX_OFED driver functionality
- Want minimal installation footprint
- Don't require DOCA SDK features
- Are migrating from MLNX_OFED

## Alternative Extensions

- **nvidia-doca-networking** - Includes DOCA Core, DPDK, OVS-DOCA, Flow (recommended for most users)
- **nvidia-doca-roce** - Lightweight RoCE-only installation
- **nvidia-doca-all** - Full DOCA SDK with all libraries
- **nvidia-doca-tools** - Management and diagnostic tools only

## Verification

```bash
# Check loaded kernel modules
lsmod | grep mlx

# List NVIDIA devices
lspci | grep -i mellanox

# Check RDMA devices
rdma link
```

## Resources

- [NVIDIA DOCA Documentation](https://docs.nvidia.com/doca/sdk/)
- [DOCA Profiles Guide](https://docs.nvidia.com/doca/sdk/DOCA-Profiles/)
