# na_epic_nva_storage

This role can be used to set up the storage component for running an FIO or GenIO test. It creates vservers, activates protocols, creates volumes, creates LIFs, and creates the appropriate components depending on the protocol. For NVME it sets up namespaces and subsystems. For FCP it sets up LUNs and igroups. In addition, it collects node and aggregate information that is used to set up the LIFs.

## Requirements

All of the requirements to run ONTAP modules are also required to run this role. Those requirements as well as additional requirements are listed below.

- Needs aggregates to already be created
- A physical or virtual clustered Data ONTAP system. The modules support Data ONTAP 9.1 and onward.
- Ansible 2.6
- Ansible 2.9 or later is strongly recommended as it enables the new collection delivery system.
- Python2 netapp-lib (2017.10.30) or later. Install using ‘pip install netapp-lib’
- Python3 netapp-lib (2018.11.13) or later. Install using ‘pip install netapp-lib’

## Role Variables

### Required Variables

| Variable    | Description                                                                                                                                               |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| protocol    | This is an argument that must be passed. The current options are nvme or fcp, with iscsi to be added later.                                               |
| storage_num | This field is used to keep track of different HA pairs and is usually set in the host file.                                                               |
| port_num    | This field is used to specify which ports are connected to fiber channel. If your fiber channel ports are 3a through 3d, you should pass in the number 3. |

### General Use Variables

| Variable                 | Default | Description                                                                                                       |
| ------------------------ | ------- | ----------------------------------------------------------------------------------------------------------------- |
| lifs_per_vserver         | 4       | This setting is used to change the number of lifs per vserver. With 4 it will create 2 direct and 2 remote links. |
| vserver_count            | 4       | This is the number of vservers that will be created.                                                              |
| vserver_ipspace          | Default | This sets the ip space setting for the vserver.                                                                   |
| vserver_security_style   | unix    | This sets the vserver security style.                                                                             |
| vserver_snap_policy      | none    | This sets the vserver snapshot policy.                                                                            |
| volume_count_per_vserver | 8       | This sets the number of volumes per vserver. With 4 vservers it will create a total of 32 volumes.                |
| volume_size              | 1228    | This sets the size of the volumes when combined with the volume_size_unit setting.                                |
| volume_size_unit         | gb      | This sets the unit for the size of the volumes.                                                                   |
| volume_policy            | default | This sets the volume policy.                                                                                      |
| volume_snapshot_policy   | none    | This sets the snapshot policy for the volumes.                                                                    |
| volume_space_guarantee   | none    | This sets the volume space guarantee settings.                                                                    |

### Protocol Specific Variables

- NVME/FC

| Variable                    | Default       | Description                                                |
| --------------------------- | ------------- | ---------------------------------------------------------- |
| namespace_count_per_vserver | 8             | This sets how many namespaces will be created per vserver. |
| namespace_size              | 1000000000000 | This is how large the namespaces will be in bytes.         |
| namespace_block_size        | 4096          | This is the block size of the namespaces.                  |
| namespace_ostype            | linux         | This is the ostype for the namespaces.                     |
| namespace_policy            | default       | This is the namespace policy.                              |
| namespace_snapshot_policy   | none          | This is the namespace snapshot policy.                     |
| namespace_space_guarantee   | none          | This is the namespace space guarantee.                     |

- FCP

| Variable              | Default | Description                                                                         |
| --------------------- | ------- | ----------------------------------------------------------------------------------- |
| lun_count_per_vserver | 8       | This sets how many luns will be created per vserver.                                |
| lun_size              | 1000    | This is the size of the luns. Used with lun_size_unit to determine the actual size. |
| lun_size_unit         | gb      | This is the size unit for the luns.                                                 |
| lun_ostype            | linux   | This is the os type of the luns                                                     |

## Dependencies

This role is not dependent on any other Ansible roles. Only dependency is the netapp.ontap collection.

## Example Playbook

Example usage showing how you could use the role to set up NVME.

    ---
    - hosts: storage
      vars:
        state: present
        protocol: nvme
        host_nqns:
          - "nqn.id-1"
          - "nqn.id-2"
          - "nqn.id-3"
          - "nqn.id-4"
      roles:
        - epic-storage

Example usage showing how you could use the role to set up FCP.

    - hosts: storage
      vars:
        state: present
        protocol: fcp
        initiators:
          - 10:00:00:00:00:00:00:00
          - 10:00:00:00:00:00:00:00
          - 10:00:00:00:00:00:00:00
          - 10:00:00:00:00:00:00:00
          - 10:00:00:00:00:00:00:00
          - 10:00:00:00:00:00:00:00
          - 10:00:00:00:00:00:00:00
          - 10:00:00:00:00:00:00:00

      roles:
        - na_epic_nva_storage

Inventory host file example (this is using the yaml host format but the more INI format will also work)

    storage:
      children:
        db_storage:
          hosts:
            10.0.0.1:
              name: epic_storage1
              ansible_connection: local
              storage_num: 1
              port_num: 3
              ntap_username: username
              ntap_password: password
        jrn_storage:
          hosts:
            10.0.0.2:
              name: epic_storage2
              ansible_connection: local
              storage_num: 2
              port_num: 2
              ntap_username: username
              ntap_password: password

## License

BSD

## Author Information

- [Chris Schmitt](mailto:Chris.Schmitt@netapp.com)
