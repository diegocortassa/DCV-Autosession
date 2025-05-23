# Autosession for DCV
Autosession for DCV is a configuration script that creates a simple standalone Client Server architecture where the DCV sessions (console or virtual) life cycles are automatically managed without the need for additional running services, management interfaces, or servers.
The installation script should be idempotent and it can be executed many times with the same outcome, it is safe to put it in an ansible playbook.

NOTE: If login doesn't work with sssd + active directory change access_provider to simple

# Prerequisites
This script is intended for  Red Hat Enterprise Linux 9 or clones (Rocky Linux 9, AlmaLinux 9) and it assumes you have a graphical installation with the Gnome Desktop environment and GDM3 display manager, basically the "Workstation" or "Server with GUI" software selection during the OS installation.

It should be easy to adapt it to Ubuntu or other Linux distributions.

# BETA!
This script is in beta stage, use it at your own risk.

Bug reports and pull requests are welcome!

# How it works
When a user successfully logs in with the native or web client
- A pam_exec PAM module launches the dcv_autosessions.sh script
- the dcv_autosessions.sh script
    - If no session exists it creates a dynamic virtual or console (configurable) session
    - If the same user session is already running reconnect it
    - If another user session is running it asks the logged-in user to accept a collaboration with two options:
        - View only, where the collaborator will only see the user screen
        - Full control where the collaborator will be able to interact with the desktop

# Installed files list
```
/etc/dcv/dcv.conf
/etc/dcv/default.perm
/etc/dcv/dcv_autosessions.env
/etc/pam.d/dcv-autosession
/etc/X11/xorg.conf.d/20-dcv-stylus.conf
/usr/bin/dcv_autosession.sh
/usr/bin/dcv_collab_prompt.sh
/usr/bin/dcv_unlock_kering.sh
/etc/xdg/autostart/unlock_keyring.desktop
/etc/polkit-1/localauthority/50-local.d/45-allow-colord.pkla
/etc/polkit-1/localauthority/50-local.d/50-allow-reboot.pkla
```

# Credits
The dcv_collab_prompt.sh script was taken from https://github.com/NISP-GmbH/DCV-Management-Linux and slightly modified

# TODO
- A virtual session login does not update wtmp/utmp (last, who, and w commands do not work) dcvpamhelper does not seem to call PAM for session.
- When using console sessions if the user logs out and disconnects the session stays open (does dcv call pam session at logout?)
- Should we add a monitor script (cron or service) to close unused console sessions?
- google-chrome-stable-135.0.7049.84-1.x86_64 seg faults in virtual session even with "--disable-gpu" 
- google-chrome-stable-122.0.6261.128-1.x86_64 and google-chrome-stable-131.0.6778.139-1.x86_64 work ok
