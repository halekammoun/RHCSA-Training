
https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/using_the_desktop_environment_in_rhel_8/assembly_installing-applications-using-flatpak_using-the-desktop-environment-in-rhel-8#searching-for-flatpak-applications_assembly_installing-applications-using-flatpak
<h1 align="center" style="color: red;">Configure Flatpak Repositories</h1>

## Introduction

👋 In this section, we will learn how to configure a **Flatpak** repository and install applications.

Unlike **DNF**, which installs RPM packages as part of the operating system, **Flatpak** is mainly used to install desktop applications such as **VSCodium**, **Discord**, **Spotify**, or **GIMP**.

Flatpak supports two installation modes:

* **System installation:** available for all users.
* **User installation:** available only for a specific user using the `--user` option.

For the RHCSA exam, you may be asked to configure a Flatpak repository **only for one user**.

---

## Main Commands

* `flatpak --version`: Display the Flatpak version.
* `flatpak remotes`: List all configured repositories.
* `flatpak remotes --user`: List repositories configured for the current user.
* `flatpak remote-add --user <name> <url>`: Add a repository for the current user.
* `flatpak search <application>`: Search for an application.
* `flatpak install --user <repository> <application>`: Install an application for the current user.
* `flatpak list --user`: List installed applications for the current user.
* `flatpak info <application>`: Display information about an installed application.
* `flatpak uninstall --user <application>`: Remove an application.
* `flatpak remote-delete --user <repository>`: Remove a repository.

---

## Installation Modes

| System Installation          | User Installation                  |
| ---------------------------- | ---------------------------------- |
| Available for all users      | Available only for one user        |
| Stored in `/var/lib/flatpak` | Stored in `~/.local/share/flatpak` |
| Requires root privileges     | No root privileges required        |
| Default installation         | Use the `--user` option            |

---

## Lab 10

#### Q0. Configure Flatpak so that the **student** user can install **VSCodium** from the Flathub repository.

Requirements:

* Repository URL:

  ```
  https://flathub.org/repo/flathub.flatpakrepo
  ```
* Repository name: **flatdb**
* Configure the repository **only** for the **student** user.
* Install the **VSCodium** application.

```bash
su - student

flatpak remote-add --user flatdb \
https://flathub.org/repo/flathub.flatpakrepo

flatpak search codium

flatpak install --user flatdb com.vscodium.codium

flatpak list --user
```

#### Verify the repository

```bash
flatpak remotes --user
```

Expected output:

```text
Name      Options
flatdb    user
```

#### Verify the installation

```bash
flatpak info com.vscodium.codium
```

<p style="text-align: right;">
  <a href="https://github.com/halekammoun/RHCSA-Training/blob/main/README.md#table-des-matieres">Back to Table of Contents</a>
</p>
