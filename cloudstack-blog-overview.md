# Building an Enterprise-Grade CloudStack VXLAN Lab in the Cloud: A Technical Journey

*Author: Steven Liang | Senior Cloud Native Architect*  
*Published: September 2025*

![Lab Architecture Overview](/images/lab-overview-architecture.png)

## Introduction

Throughout my 25-year career in IT infrastructure, I've witnessed the evolution from physical servers to virtualization, and now to cloud-native architectures. As a cloud architect who has worked extensively at Broadcom (formerly VMware/Pivotal), I understand the critical importance of mastering different IaaS platforms. This blog series documents my complete journey of building an enterprise-grade CloudStack VXLAN lab on the Kamatera cloud platform.

## Why This Series?

### Technical Motivation

In modern cloud architectures, network virtualization has become a cornerstone technology. However, most CloudStack tutorials still focus on traditional VLAN configurations. When I decided to deep-dive into CloudStack's VXLAN + BGP EVPN implementation, I found that practical, hands-on documentation was extremely scarce. This motivated me to document the entire implementation process and contribute a comprehensive technical guide to the community.

### Challenges and Opportunities

Building a CloudStack lab on public cloud presents numerous challenges:

- **Network Limitations**: Kamatera's Layer 2 network doesn't support VLAN tagging
- **Resource Optimization**: Achieving enterprise features while controlling costs
- **Technical Complexity**: Integrating VXLAN, BGP EVPN with CloudStack requires deep networking knowledge
- **Lab Environment**: Creating a platform that's both production-like and learning-friendly

## Project Overview

### Architecture Vision

![Target Architecture](/images/target-architecture.png)

Our goal is to build a complete CloudStack environment with the following characteristics:

- **Modern Network Architecture**: VXLAN replacing traditional VLANs
- **Dynamic Routing**: Intelligent routing using BGP EVPN
- **High Availability Design**: 3 Hypervisor nodes ensuring redundancy
- **Secure Access**: Remote management through OpenVPN
- **Cost Optimization**: Simulating enterprise environment on public cloud

### Technology Stack

After thorough evaluation, I selected the following technology combination:

```yaml
Infrastructure Layer:
  Cloud Platform: Kamatera Cloud
  Network: Layer 2 Private Network (192.168.1.0/24)
  
Network Layer:
  Software Router: VyOS 1.5.x Stream Edition
  Protocols: BGP, VXLAN, EVPN
  VPN: OpenVPN Access Server
  
Compute Layer:
  IaaS Platform: Apache CloudStack 4.21.0.0
  Operating System: Rocky Linux 9
  Hypervisor: KVM
  
Management Layer:
  Monitoring: Prometheus + Grafana
  Logging: ELK Stack
  Automation: Ansible + Terraform
```

## Implementation Journey

### Phase 1: Network Infrastructure (Weeks 1-2)

The initial challenge was implementing complex network topology within Kamatera's constraints. After multiple attempts, I realized the need for an intermediate layer to handle all network functions.

**Key Decision Points:**
- Choosing VyOS as the networking Swiss Army knife
- Designing router-on-a-stick architecture for all traffic
- Implementing SNAT to ensure internal connectivity

### Phase 2: VyOS Configuration Optimization (Week 3)

![VyOS Network Topology](/images/vyos-topology.png)

VyOS configuration proved more complex than expected, particularly when handling:
- VXLAN tunnel endpoints
- BGP peering configuration
- OpenVPN and internal routing integration

**Breakthrough Moment**: When I successfully established the first VXLAN tunnel and saw BGP routes propagating correctly, the entire architecture's feasibility was validated.

### Phase 3: CloudStack Integration (Weeks 4-5)

Integrating CloudStack with the VXLAN network was the most challenging part:

```
Challenge Checklist:
□ CloudStack network plugin configuration
□ VXLAN to physical network mapping
□ BGP EVPN control plane integration
□ Multi-tenant isolation verification
□ Performance optimization and tuning
```

### Phase 4: Production-Grade Optimization (Week 6)

The final phase focused on elevating the lab environment to near-production quality:
- Implementing monitoring and alerting
- Configuring automated backups
- Performance benchmarking
- Security hardening

## Technical Breakthroughs and Innovations

### 1. Virtual VLAN Implementation

Since Kamatera doesn't support VLAN tagging, I developed an innovative solution:

```
Traditional Approach: Physical Switch → VLAN Tags → CloudStack
My Solution: VyOS Bridge → Virtual VLAN → VXLAN Mapping → CloudStack
```

### 2. BGP EVPN in Constrained Environments

Implementing BGP EVPN without physical switch support required creative thinking:

- Using VyOS as virtual Route Reflector
- Implementing software-defined VTEPs
- Achieving multi-tenant routing isolation through eBGP

### 3. Performance Optimization Strategies

Achieved near bare-metal network performance through:

- **CPU Affinity**: Binding network interrupts to specific CPU cores
- **DPDK Acceleration**: Enabling userspace networking on supported components
- **Cache Optimization**: Tuning Linux network stack parameters

## Key Learnings

### Technical Insights

1. **Deep Understanding of Network Virtualization**
   - VXLAN encapsulation mechanisms and overhead calculations
   - BGP EVPN route propagation principles
   - Multi-tenant network isolation implementation

2. **CloudStack Internal Mechanisms**
   - Network orchestration workflows
   - System VM communication mechanisms
   - Resource scheduling algorithms

3. **Troubleshooting Capabilities**
   - Complex network issue diagnosis
   - Performance bottleneck analysis
   - Log correlation analysis

### Architectural Thinking

This project reinforced several architectural principles:

- **Simplifying Complexity**: Breaking down complex problems into manageable components
- **Progressive Implementation**: Starting with minimum viable solution, then iterating
- **Documentation-Driven**: Thoroughly documenting every decision and configuration
- **Automation First**: Automating repetitive tasks wherever possible

## Blog Series Navigation

This series is divided into three parts, each diving deep into specific areas:

### 📘 [Part 2: VyOS - Building Intelligent Network Infrastructure](./vyos-configuration-guide.md)

Deep dive into VyOS configuration and optimization:
- VyOS 1.5.x installation and initialization
- Network interface configuration (WAN/LAN)
- SNAT and routing policy implementation
- VXLAN tunnel configuration
- BGP protocol configuration and optimization
- OpenVPN Access Server integration
- Performance tuning and monitoring

**Estimated Reading Time**: 45 minutes | **Technical Difficulty**: ★★★★☆

### 📗 [Part 3: CloudStack VXLAN Revolution - Beyond Traditional Network Boundaries](./cloudstack-vxlan-implementation.md)

Detailed coverage of CloudStack and VXLAN integration:
- Rocky Linux 9 environment preparation
- CloudStack 4.21.0.0 installation process
- VXLAN network plugin configuration
- BGP EVPN integration strategy
- Management node high availability
- Hypervisor node optimization
- Multi-tenant network implementation
- Troubleshooting and best practices

**Estimated Reading Time**: 60 minutes | **Technical Difficulty**: ★★★★★

## Project Resources

### Code Repository

All configuration files, scripts, and automation tools are hosted on GitHub:

```bash
git clone https://github.com/stevenliang/cloudstack-vxlan-lab.git
cd cloudstack-vxlan-lab

# Project Structure
├── images/                    # All architecture diagrams and screenshots
│   ├── lab-overview-architecture.png
│   ├── target-architecture.png
│   ├── vyos-topology.png
│   ├── vxlan-packet-flow.png
│   ├── bgp-evpn-topology.png
│   └── cloudstack-network-zones.png
├── vyos/                      # VyOS configuration files
│   ├── config.boot           # Complete configuration
│   ├── scripts/              # Automation scripts
│   └── monitoring/           # Monitoring configuration
├── cloudstack/               # CloudStack configuration
│   ├── installation/         # Installation scripts
│   ├── network-config/       # Network configuration
│   └── api-examples/         # API usage examples
├── ansible/                  # Ansible playbooks
│   ├── inventory/
│   ├── roles/
│   └── playbooks/
└── terraform/                # Infrastructure as Code
    ├── modules/
    └── environments/
```

### Environment Specifications

```yaml
Lab Total Resources:
  VyOS Router:
    CPU: 2 vCPUs
    RAM: 2 GB
    Storage: 20 GB SSD
    Network: 1 Public IP + Private Network
    
  CloudStack Management:
    CPU: 4 vCPUs
    RAM: 8 GB
    Storage: 100 GB SSD
    Network: Private Network (192.168.1.10)
    
  Hypervisor Nodes (x3):
    CPU: 8 vCPUs each
    RAM: 16 GB each
    Storage: 200 GB SSD each
    Network: Private Network (192.168.1.11-13)
    
  Total:
    CPU: 30 vCPUs
    RAM: 58 GB
    Storage: 720 GB
    Monthly Cost: ~$350 USD (Kamatera)
```

## Future Roadmap

### Short-term Plans (Q4 2025)

- [ ] Integrate Kubernetes with CloudStack
- [ ] Implement automated CI/CD pipeline
- [ ] Add Ceph distributed storage
- [ ] Implement zero-trust network architecture

### Long-term Vision (2026)

- **Multi-region Deployment**: Disaster recovery across Kamatera data centers
- **AI/ML Workloads**: Integrate GPU resource pools
- **Edge Computing**: Extend to edge nodes
- **Community Contribution**: Contribute VXLAN enhancements to CloudStack project

## Acknowledgments

This project's success wouldn't be possible without the open-source community:

- Apache CloudStack community for technical guidance
- VyOS development team for excellent documentation
- Kamatera for flexible cloud infrastructure
- All readers and practitioners who provided feedback

## Contact Information

If you encounter issues during implementation or want to discuss advanced use cases, feel free to reach out:

- **Email**: steven.liang@pm.me
- **LinkedIn**: [Steven Liang](https://linkedin.com/in/stevenliang)
- **GitHub**: [@stevenliang](https://github.com/stevenliang)
- **Mobile**: +65-9800-6972

## Conclusion

Building this CloudStack VXLAN lab was not just a technical challenge, but a deep exploration of modern cloud architecture. By overcoming public cloud limitations and implementing enterprise-grade network virtualization, we've proven that innovative thinking combined with solid technical skills can break traditional boundaries.

This series aims to provide the community with a complete reference implementation, helping more engineers understand and implement advanced network virtualization technologies. Whether you're new to CloudStack or an experienced architect, I hope this content provides value to your technical journey.

Remember, the best way to learn is through hands-on practice. I encourage you to build your own lab, experiment with different configurations, and even improve upon my design. Technical progress comes from continuous experimentation and sharing.

Let's push the boundaries of cloud computing together!

---

*Next: [VyOS - Building Intelligent Network Infrastructure →](./vyos-configuration-guide.md)*

**Tags**: #CloudStack #VXLAN #BGP #EVPN #VyOS #NetworkVirtualization #CloudArchitecture #IaaS #KVM #RockyLinux