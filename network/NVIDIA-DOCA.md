# NVIDIA DOCA Extensions for Talos Linux

This directory contains NVIDIA DOCA (Data Center Infrastructure On a Chip Architecture) extensions for Talos Linux, providing support for NVIDIA BlueField DPUs and ConnectX NICs.

## Available Extensions

### nvidia-doca-ofed
**Profile:** `doca-ofed` (Minimal drivers only)

Provides MLNX_OFED drivers without additional DOCA functionality. Equivalent to traditional MLNX_OFED installation.

**Use when:** You need basic driver support without DOCA SDK features.

[Documentation](nvidia-doca-ofed/README.md)

### nvidia-doca-networking
**Profile:** `doca-networking` (RECOMMENDED)

Includes MLNX_OFED drivers, DOCA Core, MLNX-DPDK, OVS-DOCA, and DOCA Flow for accelerated networking.

**Use when:** You need accelerated networking with hardware offload (most common use case).

[Documentation](nvidia-doca-networking/README.md)

### nvidia-doca-roce
**Profile:** `doca-roce` (Lightweight RoCE)

Minimal installation for RDMA over Converged Ethernet (RoCE) workloads only.

**Use when:** You only need RoCE functionality with minimal footprint.

[Documentation](nvidia-doca-roce/README.md)

### nvidia-doca-all
**Profile:** `doca-all` (Full SDK)

Complete DOCA SDK with all libraries, including DPA, security, storage, device emulation, and development headers.

**Use when:** You need advanced DOCA features or develop custom DOCA applications.

[Documentation](nvidia-doca-all/README.md)

### nvidia-doca-tools
**Tools:** Management and diagnostics

Firmware management, performance testing, and diagnostic tools for NVIDIA networking hardware.

**Use when:** You need to manage firmware, diagnose issues, or test performance.

[Documentation](../tools/nvidia-doca-tools/README.md)

## Quick Start

### 1. Choose Your Profile

| Profile | Size | Use Case | Includes |
|---------|------|----------|----------|
| **nvidia-doca-ofed** | Small | Basic drivers | MLNX_OFED only |
| **nvidia-doca-networking** | Medium | Accelerated networking | OFED + Core + DPDK + OVS + Flow |
| **nvidia-doca-roce** | Small | RoCE only | Minimal RDMA |
| **nvidia-doca-all** | Large | Full features | Complete SDK |
| **nvidia-doca-tools** | Small | Management | Tools only |

### 2. Install Extension

```yaml
machine:
  install:
    extensions:
      # Choose one driver extension
      - image: ghcr.io/siderolabs/nvidia-doca-networking:VERSION
      
      # Optionally add tools
      - image: ghcr.io/siderolabs/nvidia-doca-tools:VERSION
```

### 3. Load Kernel Modules

```yaml
machine:
  kernel:
    modules:
      - name: mlx5_core
      - name: mlx5_ib
      - name: ib_uverbs
      - name: rdma_cm
```

## Supported Hardware

### BlueField DPUs
- BlueField-3
- BlueField-2

### ConnectX NICs
- ConnectX-8
- ConnectX-7
- ConnectX-6 DX / LX / standard
- ConnectX-5
- ConnectX-4 LX

**Note:** DOCA functionality is limited by device capabilities. ConnectX devices cannot use certain features like DPA.

## Profile Comparison

### Feature Matrix

| Feature | ofed | networking | roce | all |
|---------|------|------------|------|-----|
| MLNX_OFED Drivers | ✅ | ✅ | ✅ | ✅ |
| RDMA/InfiniBand | ✅ | ✅ | ✅ | ✅ |
| RoCE | ✅ | ✅ | ✅ | ✅ |
| DOCA Core | ❌ | ✅ | ❌ | ✅ |
| MLNX-DPDK | ❌ | ✅ | ❌ | ✅ |
| OVS-DOCA | ❌ | ✅ | ❌ | ✅ |
| DOCA Flow | ❌ | ✅ | ❌ | ✅ |
| DPA (BlueField only) | ❌ | ❌ | ❌ | ✅ |
| Security Libraries | ❌ | ❌ | ❌ | ✅ |
| Storage Acceleration | ❌ | ❌ | ❌ | ✅ |
| Device Emulation | ❌ | ❌ | ❌ | ✅ |
| GPU Integration | ❌ | ❌ | ❌ | ✅ |
| Development Headers | ❌ | ❌ | ❌ | ✅ |

### Size Comparison

| Extension | Approximate Size |
|-----------|-----------------|
| nvidia-doca-ofed | ~100-150 MB |
| nvidia-doca-networking | ~200-300 MB |
| nvidia-doca-roce | ~80-100 MB |
| nvidia-doca-all | ~500 MB-1 GB |
| nvidia-doca-tools | ~50-80 MB |

## Common Configurations

### High-Performance Networking

```yaml
machine:
  install:
    extensions:
      - image: ghcr.io/siderolabs/nvidia-doca-networking:VERSION
  
  kernel:
    modules:
      - name: mlx5_core
      - name: mlx5_ib
      - name: ib_uverbs
  
  network:
    interfaces:
      - interface: eth0
        mtu: 9000
  
  sysctls:
    net.core.rmem_max: 268435456
    net.core.wmem_max: 268435456
```

### Storage with RDMA

```yaml
machine:
  install:
    extensions:
      - image: ghcr.io/siderolabs/nvidia-doca-roce:VERSION
  
  kernel:
    modules:
      - name: mlx5_core
      - name: mlx5_ib
      - name: ib_uverbs
      - name: rdma_cm
```

### BlueField DPU Development

```yaml
machine:
  install:
    extensions:
      - image: ghcr.io/siderolabs/nvidia-doca-all:VERSION
      - image: ghcr.io/siderolabs/nvidia-doca-tools:VERSION
  
  kernel:
    modules:
      - name: mlx5_core
      - name: mlx5_ib
      - name: ib_uverbs
      - name: rdma_cm
```

## Migration from MLNX_OFED

If you're migrating from MLNX_OFED:

1. **Use `nvidia-doca-ofed`** for equivalent functionality
2. **Or upgrade to `nvidia-doca-networking`** for enhanced features
3. Configuration and module names remain the same
4. Existing RDMA applications work without changes

## Troubleshooting

### Check Loaded Modules

```bash
lsmod | grep mlx
```

### Verify RDMA Devices

```bash
rdma link
ibv_devices
```

### Check DOCA Version

```bash
doca-version  # Available in networking and all profiles
```

### List Devices

```bash
lspci | grep -i mellanox
```

## Resources

- [NVIDIA DOCA Documentation](https://docs.nvidia.com/doca/sdk/)
- [DOCA Profiles Guide](https://docs.nvidia.com/doca/sdk/DOCA-Profiles/)
- [BlueField Documentation](https://docs.nvidia.com/networking/dpu-doca/)
- [MLNX_OFED Documentation](https://docs.nvidia.com/networking/display/MLNXOFEDv24101040)

## Support

For issues specific to:
- **Talos integration:** Open an issue in this repository
- **DOCA functionality:** Consult [NVIDIA DOCA Forums](https://forums.developer.nvidia.com/c/infrastructure/doca/)
- **Hardware issues:** Contact NVIDIA Support

## Version Compatibility

| Talos Version | DOCA Version | Notes |
|---------------|--------------|-------|
| v1.0.0+ | 3.2.1 LTS | Current |

Always check the extension version matches your Talos release.
