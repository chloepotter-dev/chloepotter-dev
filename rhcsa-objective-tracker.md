RHCSA (EX200) Objective Tracker

A working checklist against the ten official objective groups, tracking what's been practiced hands-on so far vs. what's left. Based on the current RHEL 10-targeted EX200 syllabus.

Legend: ✅ practiced hands-on · 🟡 partially touched · ⬜ not started 🔴 = RHEL-specific tool not available on Ubuntu (DNF/RPM instead of apt, firewalld instead of ufw, SELinux instead of AppArmor, Flatpak) — needs the Rocky/AlmaLinux/RHEL VM.

Theme 1: Shell and Software

Understand and use essential tools

✅ Shell syntax, running commands
✅ grep (used to filter process lists)
✅ File/directory operations (cd, ls, cat)
✅ ugo/rwx permissions (chmod +x)
✅ Conditional execution (if, test — via service_check.sh, using systemctl is-active/is-enabled --quiet exit codes)
✅ Default values (${1:-ssh}) — now used inside a real conditional, not just echoed
⬜ Looping constructs (for, while beyond while true)
⬜ Command substitution ($(command)) beyond $(date)
⬜ Input/output redirection beyond >> (pipes, <, 2>)
⬜ Regular expressions
⬜ SSH access (client and server side) — server side install/enable done; client-side key auth not yet covered
⬜ tar and gzip archives
⬜ Hard and soft links
⬜ System documentation (man pages, info) — known conceptually now, not yet drilled hands-on

Manage software

⬜ 🔴 Configuring RPM repositories
⬜ 🔴 Installing/removing RPM packages
⬜ 🔴 Configuring Flatpak repositories
⬜ 🔴 Installing/removing Flatpak packages
(Practiced apt-based package installs on Ubuntu — e.g. openssh-server — same concept, different tool)

Create simple shell scripts

✅ Basic script structure (#!/bin/bash, comments)
✅ Positional parameters ($1, ${1:-default})
✅ Capturing output of a background process ($!)
✅ Conditional execution (if, test) — NEW this session
⬜ Looping constructs (for, while)
⬜ Command substitution ($(command)) beyond $(date)
Theme 2: Storage and File Systems

Configure local storage

✅ Partitions (MBR, via fdisk — added a partition of type Linux LVM)
✅ Physical volumes, volume groups, logical volumes (LVM) — full pvcreate → vgcreate → lvcreate build
✅ Mounting by UUID at boot (/etc/fstab entry, tested with a real reboot)
✅ Adding storage non-destructively (second virtual disk, not touching OS disk)
⬜ GPT disks specifically (only practiced MBR so far)
⬜ Swap

Create and configure file systems

✅ ext4 (mkfs.ext4)
✅ Extending logical volumes (lvextend + resize2fs, done LIVE while mounted, no downtime)
⬜ vfat, xfs file systems
⬜ NFS mounts, autofs
⬜ set-GID directories for collaboration
⬜ Diagnosing permission problems

Status: core LVM workflow completed hands-on (Sep 2026) — built a full stack from a blank 5GB virtual disk through fdisk partitioning, pvcreate/vgcreate/lvcreate, ext4 formatting, UUID-based fstab mounting, verified persistence with an actual sudo reboot, then live-extended the volume with lvextend + resize2fs while it stayed mounted.

Theme 3: Running Systems

Operate running systems

✅ Identifying and killing intensive processes (top, ps aux, kill)
✅ Service status (systemctl status)
✅ Logs and journals (basic — systemctl status shows recent log lines)
⬜ Booting/rebooting, boot targets, interrupting boot process
⬜ Adjusting scheduling, tuning profiles
⬜ Secure file transfer (scp/rsync)

Deploy, configure, and maintain systems

✅ Enabling a service to start (systemctl enable --now ssh) — reboot-verified persistence, NEW this session
⬜ Scheduling with at and cron
⬜ Booting into a specific target automatically
⬜ Time service clients
⬜ Installing updates from a repository
⬜ Modifying the bootloader

Manage basic networking

✅ IPv4 addressing (static config via netplan → NetworkManager, reboot-verified) — NEW this session
✅ Hostname resolution (/etc/hosts, /etc/nsswitch.conf ordering, resolvectl, getent — proved files beats dns precedence hands-on) — NEW this session
🟡 Starting network services at boot — touched via SSH persistence work, not a dedicated networking-service exercise yet
⬜ IPv6 addressing
⬜ 🔴 Restricting access with firewalld/firewall-cmd
🟡 OPEN ISSUE: gateway (192.168.155.2) currently unreachable even under clean single-address DHCP config. Root cause is NOT netplan (that bug is fixed and confirmed). Suspected VMware host-side virtual network issue (NAT/DHCP service, adapter mode, or Virtual Network Editor settings) — needs checking from the Windows host, not the guest. Revisit before considering this objective fully closed.
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
Known Gotchas Log (real debugging lessons learned)
Ubuntu's SSH service is named ssh, not sshd. RHEL uses sshd. Same daemon, different unit name — don't assume the name carries over between distros.
systemd inhibitor locks can block reboots. A logged-in GUI session registers a "don't reboot" lock. Use systemctl reboot -i to override when you're sure it's safe, or systemd-inhibit --list to see what's holding it.
On Ubuntu, netplan sits in front of NetworkManager and can silently override nmcli changes. NetworkManager profiles are generated from netplan YAML in /etc/netplan/, not the other way around. nmcli connection modify can appear to succeed but get reverted on the next netplan apply.
Netplan merges ALL .yaml files in /etc/netplan/, not just the one you edit. A leftover NetworkManager-generated passthrough file (e.g. 90-NM-*.yaml) can silently reassert old settings (like a stale static IP) even after you've correctly edited the "real" config file. Always check ls /etc/netplan/ for extra files before assuming your edit is the only source of truth.
netplan apply and even a full NetworkManager restart don't always fully replace addresses — they can layer old and new together. Use sudo ip addr flush dev <iface> to force a clean slate when addresses seem to be stacking (secondary dynamic showing up alongside a static one).
This entire netplan-layering problem is Ubuntu-specific and won't exist on RHEL. RHEL's NetworkManager profiles are the actual source of truth — no netplan layer underneath to fight. Don't go looking for a phantom netplan file over there.
VMware Workstation's virtual network (NAT/DHCP) is a separate thing to troubleshoot from the guest OS's own networking. If DNS, ping, and gateway all fail even under a known-clean, single-address DHCP config, the problem is likely on the host side (VMware NAT/DHCP service, adapter mode, or Virtual Network Editor) — not something fixable from inside the VM.
Next concrete steps (in order)
✅ Clean RHCSA-style script example: conditionals + a standard admin task — done (check_user.sh, then service_check.sh with real if/test logic)
✅ Stage 5: Disks/LVM — done, including live extend and reboot-verified persistence
✅ SSH enable/persistence — done, reboot-verified
✅ Static IPv4 addressing — done, reboot-verified (with real debugging of a genuine multi-file netplan bug)
✅ Hostname resolution — done, precedence proven hands-on
🟡 Open: VMware host-side gateway/NAT connectivity issue — check Windows host VMware services and Virtual Network Editor settings
⬜ Fill remaining storage gaps: GPT disks, swap, vfat/xfs, NFS/autofs, set-GID, permission diagnosis
⬜ Fill remaining Theme 1 gaps: for/while loops, command substitution beyond $(date), man pages (hands-on drilling), vim proficiency
NEXT UP: Set up a second VM — Rocky Linux, AlmaLinux, or RHEL (free dev subscription) — for the 🔴-marked RHEL-only items, starting with Sander van Vugt's RHCSA course Lesson 1/2 labs (Getting & Installing RHEL, including custom partitioning)
⬜ Work through firewalld, SELinux, DNF/RPM, Flatpak on that RHEL-based VM specifically
⬜ IPv6 addressing, remaining "Operate/Deploy running systems" items (cron, boot targets, scp, at)
