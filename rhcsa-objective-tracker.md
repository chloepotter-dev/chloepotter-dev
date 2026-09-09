RHCSA (EX200) Objective Tracker

A working checklist against the ten official objective groups, tracking what's been practiced hands-on so far vs. what's left. Based on the current RHEL 10-targeted EX200 syllabus.

Legend: ✅ practiced hands-on · 🟡 partially touched · ⬜ not started

Note: items marked with 🔴 are RHEL-specific tools not available on Ubuntu (DNF/RPM instead of apt, firewalld instead of ufw, SELinux instead of AppArmor, Flatpak). These will need a Rocky Linux / CentOS Stream / RHEL VM specifically.

Theme 1: Shell and Software
Understand and use essential tools
✅ Shell syntax, running commands
✅ grep (used to filter process lists)
✅ File/directory operations (cd, ls, cat)
✅ ugo/rwx permissions (chmod +x)
⬜ Input/output redirection beyond >> (pipes, <, 2>)
⬜ Regular expressions
⬜ SSH access (client and server side)
⬜ tar and gzip archives
⬜ Hard and soft links
⬜ System documentation (man pages, info)
Manage software
⬜ 🔴 Configuring RPM repositories
⬜ 🔴 Installing/removing RPM packages
⬜ 🔴 Configuring Flatpak repositories
⬜ 🔴 Installing/removing Flatpak packages
(Practiced apt-based package installs on Ubuntu — same concept, different tool)
Create simple shell scripts
✅ Basic script structure (#!/bin/bash, comments)
✅ Positional parameters ($1, $2)
✅ Default values (${1:-300})
✅ Capturing output of a background process ($!)
⬜ Conditional execution (if, test)
⬜ Looping constructs (for, while beyond while true)
⬜ Command substitution ($(command)) beyond $(date)
Theme 2: Storage and File Systems
Configure local storage
✅ Partitions (MBR, via fdisk — added a partition of type Linux LVM)
✅ Physical volumes, volume groups, logical volumes (LVM) — built pvcreate → vgcreate → lvcreate from a blank disk
✅ Mounting by UUID at boot (/etc/fstab entry, tested with a real reboot)
✅ Adding storage non-destructively (attached a second virtual disk rather than touching the existing OS disk)
⬜ GPT disks specifically (only practiced MBR so far)
⬜ Swap
Create and configure file systems
✅ ext4 (mkfs.ext4)
✅ Extending logical volumes (lvextend + resize2fs, done LIVE while mounted, no downtime)
⬜ vfat, xfs file systems
⬜ NFS mounts, autofs
⬜ set-GID directories for collaboration
⬜ Diagnosing permission problems

**Status: core LVM workflow completed hands-on (Sep 2026) — built a full stack from a blank 5GB virtual disk through fdisk partitioning, pvcreate/vgcreate/lvcreate, ext4 formatting, UUID-based fstab mounting, verified persistence with an actual sudo reboot, then live-extended the volume with lvextend + resize2fs while it stayed mounted. Remaining gaps: GPT, swap, vfat/xfs, NFS/autofs, set-GID, permission troubleshooting.

Theme 3: Running Systems
Operate running systems
✅ Identifying and killing intensive processes (top, ps aux, kill)
✅ Service status (systemctl status)
✅ Logs and journals (basic — systemctl status shows recent log lines)
⬜ Booting/rebooting, boot targets, interrupting boot process
⬜ Adjusting scheduling, tuning profiles
⬜ Secure file transfer (scp/rsync)
Deploy, configure, and maintain systems
✅ Enabling a service to start (systemctl start — but not yet enable for boot persistence)
⬜ Scheduling with at and cron
⬜ Booting into a specific target automatically
⬜ Time service clients
⬜ Installing updates from a repository
⬜ Modifying the bootloader
Manage basic networking
⬜ IPv4/IPv6 addressing
⬜ Hostname resolution
⬜ Starting network services at boot
⬜ 🔴 Restricting access with firewalld/firewall-cmd
Theme 4: Identity and Security
Manage users and groups
✅ Creating a local account (useradd)
✅ Local groups and memberships (viewed in WebUX)
⬜ Deleting/modifying accounts
⬜ Password aging
⬜ Configuring superuser access (sudo group config, not just using sudo)
Manage security
⬜ 🔴 Firewall settings (firewalld/firewall-cmd)
⬜ Default file permissions (umask)
⬜ Key-based SSH authentication
⬜ 🔴 SELinux enforcing/permissive modes
⬜ 🔴 File and process contexts, restoring defaults
⬜ 🔴 Port labels, boolean settings
The one rule that changes everything

Configurations must persist after a reboot without intervention. A change made only in memory scores zero, however correct it looked at the time.

Habit to build once storage/networking work starts: reboot on purpose during practice, before assuming a task is done.

Next concrete steps (in order)
✅ Finish a clean RHCSA-style script example: conditionals + a standard admin task — done (check_user.sh)
✅ Stage 5: Disks/LVM — done, including live extend and reboot-verified persistence
Fill the remaining storage gaps: GPT disks, swap, vfat/xfs, NFS/autofs, set-GID, permission diagnosis
Set up a second VM: Rocky Linux or CentOS Stream, for the 🔴-marked RHEL-only items
Work through firewalld, SELinux, DNF/RPM, Flatpak on that RHEL-based VM specifically
Networking (Theme 3) and remaining "Operate/Deploy running systems" items (cron, boot targets, scp)
