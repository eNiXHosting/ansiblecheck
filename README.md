AnsibleCheck
=============

[![Build Status](https://travis-ci.org/AnsibleCheck/ansiblecheck.svg?branch=master)](https://travis-ci.org/AnsibleCheck/ansiblecheck)
[![Docker Automated build](https://img.shields.io/docker/automated/ansiblecheck/ansiblecheck.svg?maxAge=2592000)](https://hub.docker.com/r/ansiblecheck/ansiblecheck/)
[![Docker Pulls](https://img.shields.io/docker/pulls/ansiblecheck/ansiblecheck.svg)](https://hub.docker.com/r/ansiblecheck/ansiblecheck/)


A One Stop Solution For Checking Your Ansible Roles and Playbooks.

### General Principles

AnsibleCheck utilizes Docker to deploy test boxes with Ansible built into the
images. By then loading your generated roles into these docker instances you
images you can check to insure compliance with the platforms that you claim your
role supports.

In this project we utilize the same naming conventions that are offered in
an Ansible galaxy project as well as more standard conventions by secondary tags.

Differing platforms have differing initialization scripts. None of these images
are intended for production use. They are entirely build for the purpose of
testing Ansible roles and playbooks.

If this is intended for automated testing, then travis builds are highly preferable
because they allow you to demonstrate on your galaxy role that it can actually
support the operating systems you claim to support.


### How To Use

#### Travis-CI

To get started with travis-ci take a look at the examples in the docs. At the
end of this section will be a complete list of supported environment combinations
to generate valid travis builds of your roles.
However ```distribution``` and ```distribution_version``` will be primarily
matching the full list of Operating systems by ansible galaxy that is at the
very bottom of this document.

Basically build your role and depending on how you would like to test your [role](docs/examples/simple) or [playbook](docs/examples/simple-playbook),
either simply or with multiple tests or with [extra variables](docs/examples/with-variables)
 to assess whether your role/playbook runs as expected under all conditions.

#### Manually

1.  [Install Docker](https://docs.docker.com/engine/installation/)
2.  Pull the desired image/images from Docker Hub. These images are tags that
are in all lower case.

```shell
docker pull ansiblecheck/ansiblecheck:ubuntu-xenial
```

3.  Run a container from the image and mount the role/playbook into the image.
Check run_opts and init for the travis build

```shell
docker run --detach --volume="${PWD}":/etc/ansible/roles/role_under_test:ro \
--privileged \
--volume=/sys/fs/cgroup:/sys/fs/cgroup:ro ansiblecheck/ansiblecheck:ubuntu-xenial \
/usr/lib/systemd/systemd
```

4.  Finally Utilize Ansible Inside of the Container Space To See That The Role
functions correctly.

```shell
docker exec --tty "${container_id}" env TERM=xterm ansible-playbook \
 /etc/ansible/roles/role_under_test/tests/test.yml --syntax-check
```

```shell
docker exec "$(cat ${container_id})" ansible-playbook \
 /etc/ansible/roles/role_under_test/tests/test.yml
```

#### The current list of combinations for ansiblecheck.

You can comment out an environment with # on each line of the list item.

```yaml
- distribution: Ubuntu
  distribution_version: yakkety
  init: /lib/systemd/systemd
  run_opts: "--privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro"
- distribution: Ubuntu
  distribution_version: xenial
  init: /lib/systemd/systemd
  run_opts: "--privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro"
- distribution: Ubuntu
  distribution_version: trusty
  init: /sbin/init
  run_opts: ""
- distribution: Ubuntu
  distribution_version: precise
  init: /sbin/init
  run_opts: ""
- distribution: EL
  distribution_version: "7"
  init: /usr/lib/systemd/systemd
  run_opts: "--privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro"
- distribution: EL
  distribution_version: "6"
  init: /sbin/init
  run_opts: ""
- distribution: Debian
  distribution_version: jessie
  init: /lib/systemd/systemd
  run_opts: "--privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro"
- distribution: Debian
  distribution_version: wheezy
  init: /sbin/init
  run_opts: ""
- distribution: Fedora
  distribution_version: "24"
  init: /usr/lib/systemd/systemd
  run_opts: "--privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro"
- distribution: Fedora
  distribution_version: "23"
  init: /usr/lib/systemd/systemd
  run_opts: "--privileged --volume=/sys/fs/cgroup:/sys/fs/cgroup:ro"
```

#### Current List of Operating System Roles For Ansible Galaxy

```yaml
platforms:
  - name: OpenBSD
    versions:
      - all
      - 5.6
      - 5.7
      - 5.8
      - 5.9
      - 6.0
  - name: Fedora
    versions:
      - all
     - 16
     - 17
     - 18
     - 19
     - 20
     - 21
     - 22
     - 23
     - 24
  - name: DellOS
    versions:
      - all
      - 10
      - 6
      - 9
  - name: MacOSX
    versions:
      - all
      - 10.10
      - 10.11
      - 10.12
      - 10.7
      - 10.8
      - 10.9
  - name: Junos
    versions:
      - all
      - any
  - name: GenericBSD
    versions:
      - all
      - any
  - name: Void Linux
    versions:
      - all
      - any
  - name: GenericLinux
    versions:
      - all
      - any
  - name: NXOS
    versions:
      - all
      - any
  - name: IOS
    versions:
      - all
      - any
  - name: Amazon
    versions:
      - all
      - 2013.03
      - 2013.09
      - 2016.03
  - name: ArchLinux
    versions:
      - all
      - any
  - name: FreeBSD
    versions:
      - all
      - 10.0
      - 10.1
      - 10.2
      - 10.3
      - 8.0
      - 8.1
      - 8.2
      - 8.3
      - 8.4
      - 9.0
      - 9.1
      - 9.1
      - 9.2
      - 9.3
  - name: Ubuntu
    versions:
      - all
      - lucid
      - maverick
      - natty
      - oneiric
      - precise
      - quantal
      - raring
      - saucy
      - trusty
      - utopic
      - vivid
      - wily
      - xenial
  - name: Debian
    versions:
      - all
      - etch
      - jessie
      - lenny
      - sid
      - squeeze
      - stretch
      - wheezy
  - name: EL
    versions:
      - all
      - 5
      - 6
      - 7
  - name: Windows
    versions:
      - all
      - 2012R2
  - name: SmartOS
    versions:
      - all
      - any
  - name: opensuse
    versions:
      - all
      - 12.1
      - 12.2
      - 12.3
      - 13.1
      - 13.2
  - name: SLES
    versions:
      - all
      - 10SP3
      - 10SP4
      - 11
      - 11SP1
      - 11SP2
      - 11SP3
      - 11SP4
      - 12
      - 12SP1
  - name: GenericUNIX
    versions:
      - all
      - any
  - name: Solaris
    versions:
      - all
      - 10
      - 11.0
      - 11.1
      - 11.2
      - 11.3
  - name: eos
    versions:
      - all
      - Any
```

#### Projects Using AnsibleCheck

Submit a pull request to add your project to the list.

*   Universal Java Installer [ChristopherDavenport.universal-java](https://galaxy.ansible.com/ChristopherDavenport/universal-java/)
*   Universal Oracle Client [ChristopherDavenport.universal-oracle-client](https://galaxy.ansible.com/ChristopherDavenport/universal-oracle-client/)
*   International Components for Unicode [ChristopherDavenport.icu](https://galaxy.ansible.com/ChristopherDavenport/icu/)
*   GnuCOBOL [ChristopherDavenport.gnu-cobol](https://galaxy.ansible.com/ChristopherDavenport/gnu-cobol/)
