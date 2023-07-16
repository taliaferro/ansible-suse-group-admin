Group admin role for SUSE-like Linux distributions
=========

The default configuration for OpenSUSE puts all administrative powers in the hands of the `root` user, such that any ordinary user who wants to adminstrate the system must know `root`'s password.
This differs from the configuration in some other distributions, where the root user is locked, but users in a special group (usually `sudo` or `wheel`) can escalate privileges to root temporarily.

The group administration permissions model has a number of advantages over the single root account, particularly in enterprise settings where there may be an entire IT team that needs to make system changes. In these environments, simply sharing a single static password might be an inconvenience and a potential security risk.

This role changes the default behavior for all privilege escalation agents in OpenSUSE (`sudo`, `kdesu`, and `polkit`) to allow users in an administrative group (by default `wheel`) to escalate privileges to root.

> :warning: Warning: this has not been tested on OpenSUSE MicroOS or SUSE ALP. Those distributions use a read-only root filesystem, and the system configuration changes that this role makes may not persist on those distributions without additional steps.


> :warning: This is not fully functional on GNOME desktops. On KDE, YaST uses `kdesu` for privilege escalation, which can be made to respect your system sudoers file. However, on GNOME it uses `gnomesu`, which is specific to OpenSUSE itself and has no regard for any of this configuration. Other DEs use `xdg-su` which has the same problem.
> It may be possible to fix this by editing PAM configuration -- I'll come back to this at some point.

Role Variables
--------------
- `safety_admin_user` (**no default, you must set this yourself**): This user will be added to the administrative group before making the configuration changes. **If you select a user you cannot access, you will probably be locked out of the root account.** 
- `admin_group` (default `wheel`): The group which should be allowed to escalate privileges.
- `require_password` (default `true`): Whether the admin group will be required to enter their password to escalate. Turning this off is definitely a bad idea for security, but you do you I guess.
- `lock_root_account` (default `false`): Locks the root account's password once the changes are made, which makes the `wheel` group the only way to escalate.

Example Playbook
----------------

    - hosts: all
      when: "{{ansible_os_family is 'Suse'}}"
      roles:
         - role: taliaferro.suse-group-admin
           vars:
             safety_admin_user: taliaferro
           #   admin_group: sudo

License
-------

BSD

