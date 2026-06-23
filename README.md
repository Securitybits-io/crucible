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
- `roles/gnome` - GNOME desktop and display manager.
- `roles/neovim` - NeoVim GitHub release installation.
- `roles/productivity` - everyday terminal productivity tools.
- `roles/system_management` - package update and maintenance actions.
- `roles/vscode` - Visual Studio Code repository and package installation.
- `roles/virtualization` - VirtualBox and Vagrant.
- `roles/workstation` - workstation packages and services.

## Bootstrap

Install Ansible on the control machine, then install required collections:

```powershell
ansible-galaxy collection install -r requirements.yml
```

The initial inventory targets `localhost` with Ansible's local connection. Run
the playbooks from the workstation being provisioned.

Validate connectivity:

```powershell
ansible workstations -m ansible.builtin.ping
```

Run the workstation provisioning playbook. This refreshes APT metadata, performs
a Debian dist-upgrade, runs general package maintenance, and then applies the
base, GNOME, workstation, productivity, NeoVim, VS Code, and virtualization
roles:

```powershell
ansible-playbook playbooks/workstation.yml
```

Run in check mode before applying changes:

```powershell
ansible-playbook playbooks/workstation.yml --check --diff
```

## System Management

These playbooks are kept as focused maintenance entry points even though the
main workstation provisioning playbook already runs system maintenance.

Refresh APT metadata only:

```powershell
ansible-playbook playbooks/apt-update.yml
```

Run routine package maintenance with a safe upgrade, autoremove, and autoclean:

```powershell
ansible-playbook playbooks/system-maintenance.yml
```

Run a Debian dist-upgrade and reboot when `/var/run/reboot-required` exists:

```powershell
ansible-playbook playbooks/distro-upgrade.yml
```

Update NeoVim from the latest GitHub release:

```powershell
ansible-playbook playbooks/neovim-update.yml
```

## Included Applications

Desktop environment:

- GNOME Core.
- GDM display manager.
- GNOME Tweaks.
- dconf tooling for later desktop settings such as wallpapers.
- Dark theme with slate accent color.
- `wallpaper-yellow-tree.png` installed as the default wallpaper.
- Balanced power profile.
- Automatic screen blank after 15 minutes.
- Swedish keyboard layout with English locale text.
- 24-hour clock with the Stockholm timezone from base settings.
- GNOME settings are applied to configured admin users with dconf.

Productivity tooling:

- NeoVim from the latest GitHub release tarball.
- GNU Stow.
- tmux.
- fzf.
- ripgrep.
- Visual Studio Code from Microsoft's APT repository.

Virtualization tooling:

- VirtualBox from Oracle's Debian APT repository.
- Vagrant from HashiCorp's APT repository.
