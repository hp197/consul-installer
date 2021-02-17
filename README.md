# Installer for HashiCorp Consul - Automated Installation

## Automatically Download, Extract and Install Latest or Specific Version

[![release](https://img.shields.io/github/release/hp197/consul-installer.svg?colorB=2067b8)](https://github.com/hp197/consul-installer)
[![bash](https://img.shields.io/badge/language-bash-89e051.svg?style=flat-square)](https://github.com/hp197/consul-installer)
[![license](https://img.shields.io/github/license/hp197/consul-installer.svg?colorB=2067b8)](https://github.com/hp197/consul-installer)

---

The **consul-install** script automates the process of downloading and installing Consul.  It provides an ideal method for installing installing updates or a specific version.

This script detects the latest version, OS and CPU-Architecture and allows installation to local or system locations.  Optional parameters allow installing a specific version and installing to /usr/local/bin without prompting.

Example - install last pre-0.13 release: `./consul-install.sh -i 0.12.29`

Options:

- `-i VERSION`:  Install specific version
- `-a`:          Automatically use `sudo` to install to /usr/local/bin
  - allows for unattended installation via scripts or CD tools
  - can be set as default behavior by uncommenting line 14 (`sudoInstall=true`)
  - sudo password may be required unless NOPASSWD is enabled
- `-c`:          leave binary in working directory (for CI/DevOps use)
- `-h`:          help
- `-v`:          display version

This installer is similar to:
- [Packer Installer](https://github.com/robertpeteuil/packer-installer)
- [Vault Installer](https://github.com/robertpeteuil/vault-installer)
- [Terraform Installer](https://github.com/robertpeteuil/terraform-installer)

## Download and Use Locally

Download Installer

``` shell
curl -LO https://raw.github.com/hp197/consul-installer/master/consul-install.sh
chmod +x consul-install.sh
```

Download installer via my bootstrap server (iac.sh or https://iac.sh)

``` shell
curl iac.sh/consul > getinst.sh
bash getinst.sh
# installer downloaded as consul-install.sh in current directory
```

### Run local installer

``` shell
./consul-install.sh

# usage: consul-install.sh [-i VERSION] [-a] [-c] [-h] [-v]
#      -i VERSION	: specify version to install in format '' (OPTIONAL)
#      -a		: automatically use sudo to install to /usr/local/bin
#      -c		: leave binary in working directory (for CI/DevOps use)
#      -h		: help
#      -v		: display vault-install.sh version
```

## System Requirements

- System with Bash Shell (Linux, macOS, Windows Subsystem for Linux)
- `unzip` - consul downloads are in zip format
- `curl` or `wget` - script will use either one to retrieve metadata and download

Optional

- `jq` - if installed, latest version parsed from hashicorp downloads
  - Useful if latest github release differs from version on hashicorp downloads
  - Avoids github api limit of 60 requests per hour (unauthenticated)

## Script Process Details

- Determines Version to Download and Install
  - Uses Version specified by `-i VERSION` parameter (if specified)
  - Otherwise determines Latest Version
    - If `jq` installed parse version from hashicorp downloads
    - Otherwise use GitHub API to retrieve latest version
- Calculates Download URL based on Version, OS and CPU-Architecture
- Verifies URL Validity before Downloading in Case:
  - VERSION incorrectly specified with `-i`
  - Download URL Format Changed on consul Website
- Determines Install Destination
  - The destination can be specified with `-c` option, or passing `TF_INSTALL_DIR` environment variable
  - The default is `/usr/local/bin` if it is writable, or with `-a`
  - Otherwise the user is prompted for options
  - Performed before Download/Install Process in case user selects `abort`
- Installation Process
  - Download, Download SHA, Verify SHA of zip, Extract, Install, Cleanup and Display Results

### CPU Architecture Detection

CPU architecture is detected for each OS accordingly:

- Linux / Windows (WSL since this is a Bash script)
  - detected with `lscpu` or by inspecting `/proc/cpuinfo`
  - Arm variants use `arm` as it's the only `arm` version available (for now)
- macOS - uses Default Arch `amd64` as it's the only version available on macOS
- Default Value - `amd64`

## Disclaimer

This is a simpel clone of https://github.com/robertpeteuil/terraform-installer.
Only thing I did was a search&replace to consul.

All credits belong to Robert Peteuil

## License

Apache 2.0 License - Copyright (c) 2020    Robert Peteuil
Apache 2.0 License - Copyright (c) 2021    Henry Paulissen
