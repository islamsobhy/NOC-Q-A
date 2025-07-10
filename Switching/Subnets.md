# Subnet Masks and IP Address Allocation

## 1. What is a Subnet Mask?

A **subnet mask** is a 32-bit number that distinguishes the network portion of an IP address from the host portion. It works in conjunction with an IP address to define the network a device belongs to and to enable efficient routing of data within and between networks.

* **Binary Representation:** Like an IP address, a subnet mask is often represented in dotted-decimal notation (e.g., 255.255.255.0), but its function is clearer in binary. In binary, a subnet mask consists of a series of contiguous '1's followed by a series of contiguous '0's.
    * The '1's indicate the **network portion** of the IP address.
    * The '0's indicate the **host portion** of the IP address.
* **Logical AND Operation:** When an IP address is logically ANDed with its subnet mask, the result is the **network address** (or network ID). This network address identifies the specific subnet.

## 2. CIDR Notation (Classless Inter-Domain Routing)

CIDR notation is a more concise way to represent a subnet mask. It appends a `/` followed by the number of '1's in the subnet mask to the IP address.

* **Example:**
    * `192.168.1.0/24` means the first 24 bits of the IP address are the network portion.
    * This is equivalent to a subnet mask of `255.255.255.0` (24 ones: `11111111.11111111.11111111.00000000`).

## 3. How Subnet Masks Determine IP Address Allocation

The number of '0's in the binary representation of the subnet mask (or the number of bits remaining after the CIDR prefix) determines the size of the host portion of the IP address. This directly impacts how many usable IP addresses are available within that subnet.

Let 'H' be the number of host bits (the number of '0's in the subnet mask).

* **Total IP Addresses in a Subnet:** The total number of IP addresses that can exist within a given subnet is calculated as $2^H$.
* **Usable IP Addresses:** From the total IP addresses, two special addresses are reserved and cannot be assigned to hosts:
    * **Network Address:** The first address in the range, where all host bits are '0'. This identifies the subnet itself.
    * **Broadcast Address:** The last address in the range, where all host bits are '1'. This address is used to send data to all devices within that specific subnet.
    * Therefore, the number of **usable IP addresses** for hosts is calculated as $2^H - 2$.

## 4. Examples of Subnet Masks and IP Counts

Let's look at common CIDR prefixes and their corresponding subnet masks, host bits, total IPs, and usable IPs:

| CIDR Prefix | Subnet Mask (Dotted-Decimal) | Binary (Host Bits) | Number of Host Bits (H) | Total IPs ($2^H$) | Usable IPs ($2^H - 2$) |
| :---------- | :--------------------------- | :----------------- | :---------------------- | :---------------- | :-------------------- |
| `/30`       | 255.255.255.252              | `...11111100`      | 2                       | 4                 | 2                     |
| `/29`       | 255.255.255.248              | `...11111000`      | 3                       | 8                 | 6                     |
| `/28`       | 255.255.255.240              | `...11110000`      | 4                       | 16                | 14                    |
| `/27`       | 255.255.255.224              | `...11100000`      | 5                       | 32                | 30                    |
| `/26`       | 255.255.255.192              | `...11000000`      | 6                       | 64                | 62                    |
| `/25`       | 255.255.255.128              | `...10000000`      | 7                       | 128               | 126                   |
| `/24`       | 255.255.255.0                | `...00000000`      | 8                       | 256               | 254                   |
| `/23`       | 255.255.254.0                | `..11111110.00000000` | 9                       | 512               | 510                   |
| `/22`       | 255.255.252.0                | `..11111100.00000000` | 10                      | 1024              | 1022                  |
| `/16`       | 255.255.0.0                  | `..0.0`            | 16                      | 65,536            | 65,534                |
| `/8`        | 255.0.0.0                    | `0.0.0`            | 24                      | 16,777,216        | 16,777,214            |
