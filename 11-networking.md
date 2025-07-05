
# Networking

## Introduction

### nmtui (Text UI for NetworkManager)
👋 In this section, we will explore how to modify some network parameters.

- Change hostname: `hostnamectl hostname name.example.com`
- Use GUI with `nmtui` to set the system hostname
- Edit network settings with `nmtui` > Edit a connection > Set IPs, Gateway, DNS manually
- Don't forget to deactivate and reactivate the interface afterward

```bash
nmtui
-> edit a connection -> manual settings -> apply
-> deactivate -> activate
```

### Target (Runlevel)
- `systemctl isolate multi-user.target`: switch to multi-user mode
- `systemctl isolate graphical.target`: switch to graphical mode
- `loadkeys fr`: change keyboard layout to AZERTY
- Persist default target after reboot:
  - `systemctl set-default graphical.target`
  - `systemctl set-default multi-user.target`
- Check current default: `systemctl get-default`

### Tuned (Performance Profiles)
- Install tuned: `dnf install tuned`
- See recommended profile: `tuned-adm recommend`
- View active profile: `tuned-adm active`
- List available profiles: `tuned-adm list`
- Change profile: `tuned-adm profile <profile>`
- Restart tuned service: `systemctl restart tuned`
- Verify active profile: `tuned-adm active`

## Lab 09

#### Q0. Set the recommended profile as tuned profile
```bash
tuned-adm recommend
tuned-adm profile recommended-profile
systemctl restart tuned
tuned-adm active
```
