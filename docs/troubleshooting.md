# Troubleshooting Notes

## Ubuntu Memory Failure

### Problem

An earlier Ubuntu Server installation experienced repeated Out Of Memory events followed by a kernel panic reporting that the system was deadlocked on memory.

Symptoms included:

- Processes being killed unexpectedly
- Out Of Memory messages
- Terminal becoming unresponsive
- Kernel panic
- Memory deadlock message

### Investigation

The problem occurred on a newer Ubuntu release while running as a Hyper-V virtual machine.

The VM had enough assigned memory on paper, but the guest still experienced severe memory instability.

### Resolution

The VM was rebuilt using Ubuntu Server 24.04 LTS.

Hyper-V Dynamic Memory was disabled and the VM was assigned 8 GB of fixed memory.

The rebuilt server remained stable.

### Lesson Learned

For infrastructure labs, using a mature LTS release and predictable resource allocation can reduce unnecessary troubleshooting variables.

---

## Netplan File Permissions

### Problem

Netplan warned that the configuration file permissions were too open.

### Resolution

The permissions were corrected with:

    sudo chmod 600 /etc/netplan/99-devops-lab.yaml

Permission mode `600` restricts the file so that only root can read and write it.

### Lesson Learned

Network configuration files should use restrictive permissions because they may contain sensitive or privileged system settings.

---

## SSH Disconnect During Netplan Changes

### Problem

Running:

    sudo netplan try

while connected through MobaXterm caused the SSH session to disconnect.

### Cause

Netplan temporarily reloaded the network interfaces, interrupting the active SSH connection.

### Resolution

Potentially disruptive network changes were applied from the Hyper-V console instead of through SSH.

### Lesson Learned

Avoid making disruptive network changes over SSH unless another management path is available.

---

## Hyper-V Internal Switch Creation Error

### Problem

Creating an Internal Hyper-V switch with:

    New-VMSwitch -SwitchName "DevOps-Lab" -SwitchType Internal

failed with error:

    0x800700B7
    Cannot create a file when that file already exists.

### Investigation

The following commands were used to inspect the existing Hyper-V networking configuration:

    Get-VMSwitch

and:

    Get-NetAdapter -IncludeHidden

No usable `DevOps-Lab` Internal switch or orphaned adapter was found.

### Resolution

The lab design was changed to use:

- Hyper-V Default Switch for internet connectivity
- A dedicated Private switch named `DevOps-Lab` for internal VM-to-VM traffic

The Private switch was created with:

    New-VMSwitch -SwitchName "DevOps-Lab" -SwitchType Private

### Lesson Learned

A dual-NIC design using the Default Switch for internet access and a Private switch for stable lab communication provides a clean and predictable Hyper-V lab architecture.
