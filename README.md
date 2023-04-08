Ansible role: Common
====================

A collection of tasks that are common between all Debian and Ubuntu hosts.
This includes configuring apt repositories, managing extra packages,
configuring kernel options, system configuration,
and adding an SSH key to the root user's authorized key file.

Requirements
------------

To use this role, the python package `jmespath` must be installed on the host running ansible.

Role Variables
--------------

```
ENTRY POINT: main - A collection of common tasks

        A collection of tasks that are common between all Debian and
        Ubuntu hosts. This includes configuring apt repositories,
        managing extra packages, configuring kernel options, system
        configuration, and adding an SSH key to the root user's
        authorized key file.

OPTIONS (= is mandatory):

- apt_repos
        List of additional apt repos
        [Default: (null)]
        elements: dict
        type: list

        OPTIONS:

        = key
            Apt public signing key information for the repo

            type: dict

            OPTIONS:

            - id
                Apt key fingerprint
                [Default: (null)]
                type: str

            - keyring
                Path for the destination GPG keyring file to add the
                key to
                [Default: (null)]
                type: str

            = url
                URL for the apt key

                type: str

        = name
            Filename for the apt sources.list file (without extension)

            type: str

        = repo
            Repo source definition for sources.list file, in the
            following format: deb [ option1=value1 option2=value2 ]
            uri suite [component1] [component2] [...]

            type: str

- debian_mirror
        Debian apt mirror URL
        [Default: http://deb.debian.org/debian/]
        type: str

- grub_cmdline
        GRUB commandline default options
        [Default: (null)]
        elements: str
        type: list

- grub_serial_console
        Serial options to pass to GRUB, see
        https://www.gnu.org/software/grub/manual/grub/grub.html#serial
        [Default: (null)]
        type: str

- modprobe_blacklist_config_file
        Path for the file containing a list of blacklisted kernel
        modules
        [Default: /etc/modprobe.d/zz-ansible-blacklist.conf]
        type: str

- modprobe_modules
        List of kernel modules to manage
        [Default: (null)]
        elements: dict
        type: list

        OPTIONS:

        = name
            Name of the module

            type: str

        - options
            Options for the module
            [Default: (null)]
            elements: str
            type: list

        = state
            State for the module
            (Choices: blacklisted, loaded)
            type: str

- modprobe_modules_load_file
        Path for the file containing a list of kernel modules to load
        during boot
        [Default: /etc/modules-load.d/ansible.conf]
        type: str

- modprobe_options_config_file
        Path for the file containing loaded kernel modules options
        [Default: /etc/modprobe.d/zz-ansible-options.conf]
        type: str

- nic_packages
        Additional packages to install for NIC firmware support
        [Default: (null)]
        elements: dict
        type: list

        OPTIONS:

        = distributions
            Linux distributions the package should be installed on
            (use lowercase)

            elements: str
            type: list

        = module
            Install the package only when this module is present

            type: str

        = package
            Package to install

            type: str

- packages_install
        List of packages to install
        [Default: (null)]
        elements: str
        type: list

- packages_purge
        List of packages to purge
        [Default: (null)]
        elements: str
        type: list

- packages_remove
        List of packages to remove
        [Default: (null)]
        elements: str
        type: list

- packages_upgrade
        List of packages to upgrade
        [Default: (null)]
        elements: str
        type: list

- raspbian_mirror
        Raspbian apt mirror URL
        [Default: http://raspbian.raspberrypi.org/raspbian/]
        type: str

- raspi_mirror
        Raspberrypi apt mirror URL
        [Default: http://archive.raspberrypi.org/debian/]
        type: str

- ssh_rescue_pubkey
        Add this SSH key to the root user's authorized key file
        [Default: (null)]
        type: str

- ubuntu_mirror
        Ubuntu apt mirror URL
        [Default: http://archive.ubuntu.com/ubuntu/]
        type: str
```

Installation
------------

This role can either be installed manually with the ansible-galaxy CLI tool:

    ansible-galaxy install git+https://github.com/wandansible/common,main,wandansible.common
     
Or, by adding the following to `requirements.yml`:

    - name: wandansible.common
      src: https://github.com/wandansible/common

Roles listed in `requirements.yml` can be installed with the following ansible-galaxy command:

    ansible-galaxy install -r requirements.yml

Example Playbook
----------------

    - hosts: all
      roles:
         - role: wandansible.common
           become: true
           vars:
             debian_mirror: "https://mirror.fsmg.org.nz/debian/"
             ubuntu_mirror: "https://mirror.fsmg.org.nz/ubuntu/"

             apt_repos:
               - name: wand-libwandio
                 repo: >-
                   deb [signed-by=/etc/apt/keyrings/wand-libwandio.gpg]
                   https://dl.cloudsmith.io/public/wand/libwandio/deb/{{ ansible_distribution | lower }} {{ ansible_distribution_release }} main
                 key:
                   url: https://dl.cloudsmith.io/public/wand/libwandio/gpg.69A507877C4B94E8.key
                   id: 54B22D514CCD2F1060E195AD69A507877C4B94E8
                   keyring: /etc/apt/keyrings/wand-libwandio.gpg
               - name: wand-libwandder
                 repo: >-
                   deb [signed-by=/etc/apt/keyrings/wand-libwandder.gpg]
                   https://dl.cloudsmith.io/public/wand/libwandder/deb/{{ ansible_distribution | lower }} {{ ansible_distribution_release }} main
                 key:
                   url: https://dl.cloudsmith.io/public/wand/libwandder/gpg.850A47EB27EE6871.key
                   id: 9303641FB1EA5BC0E7527327850A47EB27EE6871
                   keyring: /etc/apt/keyrings/wand-libwandder.gpg
               - name: wand-libtrace
                 repo: >-
                   deb [signed-by=/etc/apt/keyrings/wand-libtrace.gpg]
                   https://dl.cloudsmith.io/public/wand/libtrace/deb/{{ ansible_distribution | lower }} {{ ansible_distribution_release }} main
                 key:
                   url: https://dl.cloudsmith.io/public/wand/libtrace/gpg.4DA49CA206D589FA.key
                   id: 34C301C4F83705A7B73E1A2A4DA49CA206D589FA
                   keyring: /etc/apt/keyrings/wand-libtrace.gpg

             grub_cmdline:
               - "default_hugepagesz=2M hugepagesz=2M hugepages=1024 hugepagesz=1G hugepages=4"
               - "iommu=pt"
               - "intel_iommu=on"

             modprobe_modules:
               - name: "dummy"
                 state: "loaded"
                 options:
                   - "numdummies=1"
