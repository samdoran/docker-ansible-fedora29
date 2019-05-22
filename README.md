# Fedora 29 Ansible Test Image #
[![Docker Cloud Automated build](https://img.shields.io/docker/cloud/automated/samdoran/fedora29-ansible.svg)](https://hub.docker.com/r/samdoran/fedora29-ansible/) ![Docker Pulls](https://img.shields.io/docker/pulls/samdoran/fedora29-ansible.svg)

This is a container for testing Ansible roles with [Molecule](https://molecule.readthedocs.io/en/stable/). It includes the latest version of Ansible available on this platform and is rebuilt regularly. It can also be used if you need to run Ansible on a specific operating system.

Note that Molecule does not use the Ansible version installed in the container when running tests.

## Build ##

To build your own version of this container, run:

    docker build -t fedora29-ansible .

## Testing a role using Molecule ##

If you don't already have a role repository, you can initialize one using this [cookiecutter template](https://github.com/samdoran/cookiecutter-ansible-role).

`molecule init template --url https://github.com/samdoran/cookiecutter-ansible-role`

Then run `molecule test` to run the full test suite against the role.

Run `molecule converge` to run the role against the test container but leave it running. This allows you to connect to the container using `molecule login` or just test running your role multiple times against an existing container.

If you already have a role being testing using Molecule, add this to the `platforms` section of your `molecule.yml` to use this image:

```yaml
platforms:
  - name: role-test
    image: "samdoran/${MOLECULE_DISTRIBUTION:-fedora29}-ansible:latest"
    command: ${MOLECULE_COMMAND:-""}
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
    privileged: yes
    pre_build_image: yes
```

The important parts are `pre_build_image: yes` and the `image` name. Molecule allows you to use environment variables in the configuration file, which is very handy. This allows you to easily change the container that molecule uses for tests (among other things) simply by changing the enivorment variable.

```
env MOLECULE_DISTRIBUTION=debian9 molecule test
