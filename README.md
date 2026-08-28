# crucible

Ansible configuration for turning a base Debian installation into a workstation.

## Layout

- `ansible.cfg` - local Ansible defaults for this repository.
- `inventories/workstation/hosts.yml` - workstation inventory.
- `inventories/workstation/group_vars/workstations.yml` - host-specific policy and package defaults.
- `playbooks/workstation.yml` - main provisioning playbook.
- `playbooks/apt-update.yml` - refresh APT package metadata.
- `playbooks/system-maintenance.yml` - perform routine package maintenance.
- `playbooks/distro-upgrade.yml` - perform a Debian dist-upgrade and reboot if required.
- `playbooks/neovim-update.yml` - update NeoVim from the latest GitHub release.
- `roles/base` - operating-system baseline.
- `roles/nvidia` - NVIDIA GPU detection and Debian driver installation.
- `roles/gnome` - GNOME desktop and display manager.
- `roles/dotfiles` - external dotfiles clone and GNU Stow links.
- `roles/docker` - Docker Engine, Compose plugin, service, and user group.
- `roles/hashicorp` - Terraform, Nomad, Packer, and Vagrant.
- `roles/ai_cli` - Codex CLI and Claude Code CLI.
- `roles/bitwarden` - Bitwarden desktop, CLI, and Secrets Manager CLI from upstream releases.
- `roles/deskflow` - Deskflow keyboard and mouse sharing from Debian APT.
- `roles/neovim` - NeoVim GitHub release installation.
- `roles/powershell` - PowerShell from Microsoft's Debian package repository.
- `roles/productivity` - everyday terminal productivity tools.
- `roles/spotify` - Spotify desktop client repository and package installation.
- `roles/system_management` - package update and maintenance actions.
- `roles/vscode` - Visual Studio Code repository and package installation.
- `roles/virtualization` - VirtualBox and Vagrant.
- `roles/workstation` - workstation packages and services.

## Prerequisites

- Run this from the Debian workstation being provisioned.
- The user running Ansible must be a member of the `sudo` group.
- Ansible must be installed before running the playbooks.
- The playbooks use `--ask-become-pass` because `christoffer` is not configured for passwordless sudo.

## Bootstrap

Install required Ansible collections:

```bash
ansible-galaxy collection install -r requirements.yml
```

The initial inventory targets `localhost` with Ansible's local connection. Run
the playbooks from the workstation being provisioned.

Validate connectivity:

```bash
ansible workstations -m ansible.builtin.ping --ask-become-pass
```

Run the workstation provisioning playbook. This refreshes APT metadata, performs
a Debian dist-upgrade, runs general package maintenance, and then applies the
base, NVIDIA hardware support, Spotify, GNOME, workstation, productivity,
dotfiles, NeoVim, Bitwarden, Deskflow, AI CLIs, PowerShell, VS Code, HashiCorp, Docker, and virtualization roles:

```bash
ansible-playbook playbooks/workstation.yml --ask-become-pass
```

Run in check mode before applying changes:

```bash
ansible-playbook playbooks/workstation.yml --check --diff --ask-become-pass
```

## System Management

These playbooks are kept as focused maintenance entry points even though the
main workstation provisioning playbook already runs system maintenance.

Refresh APT metadata only:

```bash
ansible-playbook playbooks/apt-update.yml --ask-become-pass
```

Run routine package maintenance with a safe upgrade, autoremove, and autoclean:

```bash
ansible-playbook playbooks/system-maintenance.yml --ask-become-pass
```

Run a Debian dist-upgrade and reboot when `/var/run/reboot-required` exists:

```bash
ansible-playbook playbooks/distro-upgrade.yml --ask-become-pass
```

Update NeoVim from the latest GitHub release:

```bash
ansible-playbook playbooks/neovim-update.yml --ask-become-pass
```

## Included Applications

Hardware support:

- NVIDIA PCI GPU detection through sysfs.
- NVIDIA driver packages installed from Debian non-free repositories when NVIDIA hardware is present.
- NVIDIA driver packages kept current with APT package updates on rerun.

Desktop environment:

- GNOME Core.
- GDM display manager.
- GNOME Tweaks.
- Dash-to-Dock enabled on the left with a 55% size limit, built-in theme, and Dodge Windows autohide.
- Workspace Indicator enabled without workspace previews.
- Open Bar enabled with Mainland bar type and square bar corners.
- Spotify Controls + Track Info and Tiling Shell enabled, with Tiling Shell inner and outer gaps set to 0.
- Dash favorites set to Firefox, Files, Alacritty, Bitwarden, VS Code, VirtualBox, and Spotify.
- Unwanted GNOME packages removed through `gnome_debloat_packages`.
- dconf tooling for later desktop settings such as wallpapers.
- Dark theme with slate accent color.
- `wallpaper-yellow-tree.png` installed as the default wallpaper.
- `profile.jpg` installed as the GNOME profile picture for configured admin users.
- Balanced power profile.
- Automatic screen blank after 15 minutes.
- Swedish keyboard layout with English locale text.
- 24-hour clock with the Stockholm timezone from base settings.
- GNOME settings are applied to configured admin users with dconf.

Productivity tooling:

- NeoVim from the latest GitHub release tarball.
- Bitwarden desktop, `bw` CLI, and `bws` Secrets Manager CLI from the latest upstream release packages.
- Deskflow from Debian APT for sharing a mouse and keyboard between computers.
- Codex CLI from the latest OpenAI GitHub release archive.
- Claude Code CLI from Anthropic's APT repository.
- PowerShell from Microsoft's Debian package repository.
- WireGuard and WireGuard tools for VPN support.
- Alacritty.
- GNU Stow.
- tmux.
- fzf.
- ripgrep.
- Visual Studio Code from Microsoft's APT repository.
- Spotify from Spotify's APT repository.
- Terraform from HashiCorp's APT repository.
- Nomad from HashiCorp's APT repository.
- Packer from HashiCorp's APT repository.
- xorriso for ISO image work in later Packer builds.

Dotfiles:

- `https://github.com/Securitybits-io/.dotfiles.git` cloned to `/home/christoffer/.dotfiles`.
- GNU Stow links all top-level package directories into `/home/christoffer`.
- Set `dotfiles_stow_packages` only when a smaller explicit package list is needed.
- TPM is cloned into `/home/christoffer/.tmux/plugins/tpm`, then `install_plugins` installs plugins declared in `.tmux.conf`.

Container tooling:

- Docker Engine from Docker's APT repository.
- Docker Compose plugin.
- Docker service enabled and started.
- `christoffer` added to the `docker` group.

Virtualization tooling:

- VirtualBox from Oracle's Debian APT repository.
- Vagrant from HashiCorp's APT repository.
