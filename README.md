
# Vagrant VM Setup - Detailed Guide

This README provides comprehensive details on a Vagrant setup featuring multiple VMs, focusing on custom network configurations, port forwarding, and each VM's role. Notably, this setup leverages a gateway VM for internet access via NAT, and uses port forwarding to enable SSH access to VMs behind this gateway.

### Configuring a Custom MAC Address and Disabling Default NAT in Vagrant

In Vagrant, advanced network configurations such as setting a custom MAC address and disabling the default NAT interface are essential for creating controlled and secure virtual environments. This section explains how to achieve these configurations, particularly useful when documentation appears limited.

Consider the following line of code from a Vagrantfile:

```ruby
webserver.vm.network "private_network", mac: "080027123456", type: "dhcp", adapter: "1", auto_config: false, virtualbox__intnet: "server"
```

**Breakdown of Configuration Elements:**

- **Custom MAC Address (`mac: "080027123456"`)**: Assigns a unique identifier to the network interface. Custom MAC addresses ensure consistent and specific network configurations, crucial for network management and security.

- **DHCP Type (`type: "dhcp"`)**: Employs DHCP for dynamic IP address assignment. This setting simplifies network management by automating IP configuration.

- **Adapter Selection (`adapter: "1"`)**: Targets the first network adapter for configuration. While the first adapter is often used for the default NAT interface, here it's repurposed for a private network, demonstrating flexibility in network setup.

- **Disable Auto-Configuration (`auto_config: false`)**: This option provides manual control over the network setup, bypassing Vagrant's automatic network configuration. It's particularly valuable for complex network arrangements or specific manual setups within the VM.

- **Internal Network Connection (`virtualbox__intnet: "server"`)**: Connects the VM to an internal network named "server." Internal networks in VirtualBox are isolated, accessible only to guest VMs, offering an environment ideal for secure, contained networking.

This configuration in Vagrant with VirtualBox allows for customized VM network setups, meeting diverse requirements for development, testing, or production use. It's an example of Vagrant's capability to adapt to various networking needs, making it a robust tool for VM management.

For those seeking to implement custom MAC addresses and disable the default NAT interface in Vagrant, understanding and utilizing these settings can be pivotal. Always ensure to thoroughly test your configurations for optimal network performance and security.

## Overview of VMs

### DHCP Server (Gateway)

- **Box**: `tvlooy/openbsd-7.4-amd64`
- **Function**: Serves as a DHCP server and a NAT gateway for other VMs.
- **Hostname**: `dhcpserver`
- **Network Interfaces**:
  - Administration Network: `192.168.42.0`
  - Server Network: `192.168.42.64`
  - Employee Network: `192.168.42.128`
- **Port Forwarding**:
  - SSH access to other VMs via ports `3502` and `3503`.
  - HTTP access via port `3501`.

### Web Server

- **Box**: `freebsd/FreeBSD-13.0-STABLE`
- **Hostname**: `webserver`
- **Network**: DHCP assigned via the `server` internal network.

### Employee

- **Box**: `hluaces/ubuntu-gnome`
- **Hostname**: `employee`
- **Network**: DHCP assigned via the `employee` internal network.

### Administration

- **Box**: `hluaces/ubuntu-gnome`
- **Hostname**: `administration`
- **Network**: DHCP assigned via the `administration` internal network.

## Network and Access Configuration

### Custom Network Setup

- The VMs are set up on isolated internal networks (`administration`, `server`, `employee`).
- The `dhcp_server` VM acts as a gateway providing internet access through NAT.

### Accessing VMs

- SSH access to VMs is routed through the `dhcp_server` gateway using specific forwarded ports:
  - SSH to `webserver`: Port `3502`
  - SSH to `employee`: Port `3503`
  - SSH to `administration`: Port `3501`

### Port Forwarding Details

- The forwarded ports (`3501`, `3502`, `3503`) are used for accessing the respective VMs behind the gateway. This setup is crucial since the default NAT interface is disabled for these VMs.

## Usage Instructions

To start the VMs, execute:

```
vagrant up
```

To SSH into a specific VM, use the corresponding forwarded port. For example, to SSH into the `webserver`:

```
ssh -p 3502 user@localhost
```

Replace `user` with the VM's username.

## Requirements

- [Vagrant](https://www.vagrantup.com/downloads)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

## Troubleshooting

- Ensure Vagrant and VirtualBox are up to date.
- Verify the port forwarding settings if you encounter SSH access issues.
- Check the gateway VM's NAT configuration for internet connectivity problems.

---

*This guide is tailored for users with an intermediate understanding of Vagrant, VirtualBox, and network configurations. Adjustments may be required based on specific network setups or VM requirements.*
