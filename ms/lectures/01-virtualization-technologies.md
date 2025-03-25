# 01 - Virtualization Technologies

## Virtualization

Virtualization is the act of creating a virtual (rather than actual) version of something at the same abstraction level, including virtual computer hardware platforms, storage devices, and computer network resources

What is it:

* The transformation of the actual hardware into a shareable format is made feasible through virtualization.
* Businesses used to typically run one application per server in the days before virtual machines
* This meant that these servers would frequently have lots of unused CPU, which was extremely wasteful, especially at scale
* In an effort to make the use of hardware more efficient, virtual machines were created.



<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)  (25).png" alt=""><figcaption><p>Traditional and virtual architecture</p></figcaption></figure>

### How is it done?

• Software called hypervisors separate the physical resources from the virtual environments.

• Hypervisors can sit on top of an operating system (like on a laptop) or be installed directly onto hardware (like a server)&#x20;

• Hypervisors take your physical resources and divide them up so that virtual environments can use them.&#x20;

• Resources are partitioned as needed from the physical environment to the many virtual environments.&#x20;

• Users interact with and run computations within the virtual environment (typically called a guest machine or virtual machine).

### Hypervisor Types

• **Type 1 hypervisor**: This hypervisor runs directly on the system hardware - A “bare metal” embedded hypervisor.&#x20;

• **Type 2 hypervisor**: This hypervisor runs on a host operating system that provides virtualization services, such as I/O device support and memory management.

Examples:

Type 1 - VMware ESX/ESXi, XenServer (Citrix), Microsoft's Hyper-V, Oracle VM

Type 2 - VMware workstation/player, VMware server, Microsoft Virtual PC, Oracle VM VirtualBox, KVM

### Advantages and Benefits

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Advantages and Benefits of virtualization</p></figcaption></figure>

### Network Adapters Comparison

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)  (23).png" alt=""><figcaption><p>Network Adapters Comparison</p></figcaption></figure>

#### NAT Adapter

This is enabled by default

A guest operating system on a VM can access hosts in a physical local area network (LAN) by using a virtual NAT (Network Address Translation) device

#### NAT Network

This mode is similar to the NAT mode that you use for configuring a router.&#x20;

If you use the NAT Network mode for multiple virtual machines, they can communicate with each other via the network.

#### Bridged Adapter

Used for connecting Guest OS in VM to your physical LAN (real network)

#### Internal Network Adapter

Virtual machines whose adapters are configured to work in the VirtualBox Internal Network mode are connected to an isolated virtual network.

#### Host Only

A VM can communicate with other VMs connected to the host-only network, and with the host machine.

Host machine can access all VMs connected to the host-only network.
