
# Root Password Reset

## Steps:
- Add `init=/bin/sh` at the end of the kernel line (after the word `quiet`) to open a shell prompt.
- Remount the filesystem in read-write mode: `mount -o remount rw /`
- Change the root password: `passwd root`
- Enter the new password when prompted and confirm it.
- Create the SELinux relabeling file: `touch /.autorelabel`
- Reboot the system: `/usr/sbin/reboot -f`

You can also follow the steps in [this video](https://www.youtube.com/watch?v=lmOTDfwi4H0).
