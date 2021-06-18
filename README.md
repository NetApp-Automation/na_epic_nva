# na_epic_nva

This repository contains the automation and config used on the Epic NVA. This includes an ansible role that will set up the storage to run Epic workloads as well as server side ansible configuration scripts.

## Folders

### Roles

This folder contains the most useful functionality in this repository. The na_epic_nva_storage role can be used to configure storage to run Epic tests. This includes the setup of vservers, lifs, volumes, activating protocols, and creating protocol specific objects.

### Playbooks

This folder contains various storage and server-side playbooks that were used to configure the testbed for Epic. The server side playbooks include general-use playbooks, protocol-specific playbooks, and workload-specific playbooks.

### Tasks

This folder contains tasks that the role uses to function. These tasks are set up in a way so that they could be used in other roles or playbooks.

## Authors

- [Chris Schmitt](mailto:Chris.Schmitt@netapp.com)
