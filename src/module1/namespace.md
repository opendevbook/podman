# understand linux namespace

## Objective:
This lab will introduce you to Linux namespaces, which are essential for containerization. We'll explore how namespaces isolate processes and their resources, making them ideal for running multiple containers on a single host. You'll learn how to create and manipulate namespaces using the unshare command and gain hands-on experience with Podman, a popular containerization tool.

Lab Steps:
1. Understanding Namespaces

- What are namespaces?
    - Isolated environments for processes
    - Provide resource isolation and security
    - Types: PID, network, mount, IPC, UTS, user
- How do they work?
Each namespace has its own root filesystem, network stack, etc.
Processes within a namespace cannot see or interact with resources outside of it.

2. Creating Namespaces with unshare

- PID namespace:
    - Isolate process IDs
    - Create a child process with its own PID namespace:
```
unshare -p bash
```

- Network namespace:
    - Isolate network interfaces and addresses
    - Create a child process with its own network namespace:
```
unshare -n bash
```

- Mount namespace:
    - Isolate mount points and file systems
    - Create a child process with its own mount namespace:

```
unshare -m bash
```

- IPC namespace:
    - Isolate inter-process communication (IPC) mechanisms
    - Create a child process with its own IPC namespace:

```
unshare -i bash
```

- UTS namespace:
    - Isolate hostname and domainname
    - Create a child process with its own UTS namespace:

```
unshare -u bash
```

- User namespace:
    - Isolate user and group IDs
    - Create a child process with its own user namespace:

```
unshare -U bash
```

## Lab: Exploring IP Netnamespaces with `ip netns`

### Objective:
This lab will introduce you to IP network namespaces (netnamespaces) and how to manage them using the ip netns command. You'll learn how to create, list, delete, and interact with netnamespaces.


Lab Steps:
1. Creating a Netnamespace
- Use the add command to create a new netnamespace:
```
ip netns add mynetns
```

2. Listing Netnamespaces
- Use the list command to view all existing netnamespaces:
```
ip netns list
```

3. Entering a Netnamespace
- Use the exec command to enter a netnamespace and execute commands within it:
```
ip netns exec mynetns bash
```

    - While inside the netnamespace, you can configure network interfaces, routes, and other network-related settings.

4. Configuring Network Interfaces
- Create a virtual network interface within the netnamespace:
```
ip link add veth0 type veth peer name veth1
```

- Assign an IP address to the interface:
```
ip address add 192.168.100.1/24 dev veth0
```

- Bring up the interface:
```
ip link set veth0 up
```

5. Creating a Bridge Interface
- Create a bridge interface within the netnamespace:
```
ip link add br0 type bridge
```

- Add the virtual interface to the bridge:
```
ip link set veth0 master br0
```

- Bring up the bridge interface:
```
ip link set br0 up
```

6. Adding Routes
- Add a default route within the netnamespace:
```
ip route add default via 192.168.100.254 dev br0
```

7. Exiting a Netnamespace
Exit the netnamespace by closing the terminal window or using the exit command.

8. Deleting a Netnamespace
Use the delete command to remove a netnamespace:
```
ip netns delete mynetns
```

## ip netns vs. unshare: A Comparison

Both `ip netns` and `unshare` are tools used to create and manage isolated environments (namespaces) on Linux systems, but they serve different purposes and have distinct functionalities.

### ip netns
- Purpose: Primarily designed for network namespace management.
- Functionality:
    - Creates, lists, deletes, and manipulates network namespaces.
    - Configures network interfaces, routes, and other network-related settings within namespaces.
    - Provides a high-level interface for network namespace management.

### unshare
- Purpose: A more general-purpose tool for creating various types of namespaces, including PID, network, mount, IPC, UTS, and user namespaces.
- Functionality:
    - Creates child processes with specific namespaces.
    - Allows for granular control over namespace creation and configuration.
    - Can be used to isolate processes in a variety of ways beyond just networking.

#### Key Differences
- Scope: ip netns is specifically focused on network namespaces, while unshare can create and manage multiple types of namespaces.
- Level of Control: unshare provides more granular control over namespace creation and configuration, allowing you to specify which namespaces to isolate and how.
- Interface: ip netns offers a more user-friendly interface for managing network namespaces, while unshare is more flexible but requires a deeper understanding of namespace concepts.

#### When to Use Which
- Network Namespace Management: Use ip netns when you primarily need to create, manage, and configure network namespaces.
- General Namespace Creation: Use unshare when you need to isolate processes in a variety of ways, including PID, mount, IPC, UTS, or user namespaces.

**In summary,** ip netns is a specialized tool for network namespace management, while unshare is a more general-purpose tool for creating various types of namespaces. The best choice depends on your specific needs and the level of control you require.