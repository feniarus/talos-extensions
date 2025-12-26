# NVIDIA DOCA All Extension

This extension provides the complete NVIDIA DOCA SDK for BlueField DPUs and ConnectX NICs.

## Profile: doca-all

Full DOCA suite with all libraries, drivers, tools, and development headers for advanced data center acceleration.

## What's Included

### Core Components
- **MLNX_OFED** - Complete driver stack
- **DOCA Core** - Device abstraction, memory management, execution model
- **DOCA Log** - Logging framework

### Networking
- **DOCA Flow** - Packet processing and flow control
- **DOCA RDMA** - Remote Direct Memory Access
- **DOCA Ethernet** - Ethernet operations
- **DOCA DMA** - Direct Memory Access
- **MLNX-DPDK** - Data Plane Development Kit
- **OVS-DOCA** - Open vSwitch acceleration

### Data Processing Acceleration (DPA)
- **DOCA DPA** - Data Path Acceleration
- **Flex IO** - Flexible I/O framework
- **DOCA PCC** - Programmable Congestion Control
- **DOCA DPA Comms** - DPA communications
- **DOCA DPA Verbs** - DPA RDMA verbs

### Security
- **DOCA App Shield** - Runtime security monitoring
- **DOCA AES-GCM** - Hardware-accelerated encryption
- **DOCA SHA** - Hardware-accelerated hashing
- **IPsec Offload** - Hardware IPsec acceleration
- **MACsec Offload** - Layer 2 encryption
- **kTLS Offload** - Kernel TLS offload

### Storage & Compression
- **DOCA Compress** - Hardware compression/decompression
- **DOCA Erasure Coding** - Data redundancy
- **NVMe-oF** - NVMe over Fabrics support
- **iSER** - iSCSI Extensions for RDMA

### Device Emulation
- **DOCA DevEmu PCI** - PCI device emulation
- **DOCA DevEmu Virtio-FS** - Virtual filesystem

### Communication & Management
- **DOCA Comch** - Communication channels
- **DOCA UROM** - User-space ROM
- **DOCA Management** - Device management APIs
- **DOCA UCX** - Unified Communication X

### Telemetry & Monitoring
- **DOCA Telemetry** - Hardware telemetry
- **DOCA Telemetry Exporter** - Metrics export

### GPU Integration
- **DOCA GPUNetIO** - GPU-NIC direct communication
- **GPUDirect RDMA** - Direct GPU memory access

### Development
- **DOCA Headers** - Development headers for all libraries
- **DOCA Examples** - Sample applications
- **DOCA Documentation** - Complete SDK documentation

## Supported Hardware

- **BlueField DPUs:** BlueField-3, BlueField-2
- **ConnectX NICs:** ConnectX-8, ConnectX-7, ConnectX-6 DX/LX, ConnectX-5, ConnectX-4 LX

**Note:** DOCA functionality is limited by device capabilities. ConnectX devices cannot use certain features like DPA.

## Installation

```yaml
machine:
  install:
    extensions:
      - image: ghcr.io/siderolabs/nvidia-doca-all:VERSION
```

## When to Use This Extension

Use `nvidia-doca-all` when you:
- Need access to all DOCA SDK features
- Develop custom DOCA applications
- Require DPA (Data Path Acceleration)
- Use advanced security features (App Shield, encryption)
- Need device emulation capabilities
- Want GPU-NIC integration
- Require the complete development environment
- Deploy on BlueField DPUs with full feature set

## Use Cases

- Advanced data center infrastructure
- Custom DPU application development
- Zero-trust security implementations
- GPU-accelerated networking
- Storage acceleration and offload
- Network function virtualization (NFV)
- Software-defined networking (SDN)
- AI/ML workload acceleration

## Configuration

### Load All Required Modules

```yaml
machine:
  kernel:
    modules:
      - name: mlx5_core
      - name: mlx5_ib
      - name: ib_uverbs
      - name: rdma_cm
      - name: ib_ipoib
```

### Enable Advanced Features

```yaml
machine:
  sysctls:
    net.core.rmem_max: 268435456
    net.core.wmem_max: 268435456
    net.ipv4.tcp_rmem: "4096 87380 134217728"
    net.ipv4.tcp_wmem: "4096 65536 134217728"
    net.core.bpf_jit_harden: 1
```

## Alternative Extensions

- **nvidia-doca-ofed** - Minimal driver-only
- **nvidia-doca-networking** - Networking focus (recommended for most users)
- **nvidia-doca-roce** - RoCE-only lightweight
- **nvidia-doca-tools** - Management tools only

## Verification

```bash
# Check DOCA version
doca-version

# List all DOCA libraries
ls /usr/local/lib/libdoca*

# Check available DOCA applications
ls /usr/local/bin/doca_*

# Verify device capabilities
doca_device_query

# Check DPA support (BlueField only)
doca_dpa_query
```

## Development

This extension includes development headers for building custom DOCA applications:

```bash
# Headers location
/usr/local/include/doca/

# Example applications
/usr/local/share/doca/examples/

# Link against DOCA libraries
gcc myapp.c -ldoca_flow -ldoca_dma -o myapp
```

## Performance Considerations

⚠️ **Warning:** This is the largest DOCA extension. Consider using profile-specific extensions if you don't need all features:
- Image size: ~500MB-1GB (vs ~100-200MB for specific profiles)
- Memory footprint: Higher due to all libraries loaded
- Boot time: Slightly longer due to more modules

## Resources

- [NVIDIA DOCA Documentation](https://docs.nvidia.com/doca/sdk/)
- [DOCA All Profile](https://docs.nvidia.com/doca/sdk/DOCA-Profiles/)
- [DOCA Programming Guide](https://docs.nvidia.com/doca/sdk/DOCA-Programming-Guide/)
- [DOCA SDK Architecture](https://docs.nvidia.com/doca/sdk/DOCA-SDK-Architecture/)
- [DOCA Libraries Reference](https://docs.nvidia.com/doca/sdk/DOCA-Libraries/)
