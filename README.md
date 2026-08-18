# AdaptiveSolutions_VVF9_IMM_BHE
Ansible repository for the Adaptive Solutions Conveged Infrastructure. This release is focused on a Virtual Server Infrastructure hosted with Cisco UCS X210c M8 servers hosted with a Cisco UCS 6664 Fabric Interconnect and the Hitachi VSP One Block High End. The Ansible playbooks included are for:
- A sub-set of a buildout covering the UCS Domain and UCS Server Profile Template creation through Intersight Managed Mode (IMM) interacting with Intersight.
- Hitachi VSP One Block High End storage buildout with a Fibre Channel storage subsystem.

This repository will also include ICO custom  Ansible exported workflows for:
-  TBD . These workflows will enable users who utilize the Hitachi VSP  within ICO environments to efficiently utilize Ansible within a single workflow for large scale environments.

# Cisco and Hitachi Adaptive Solutions for Converged Infrastructure IMM configuration with Ansible

The Adaptive Solutions partnership between Cisco and Hitachi produces converged infrastructure based validated reference architectures.  These architectures feature Cisco UCS servers and Hitachi VSP storage, with connecting infrastructure involving Cisco MDS Multi-layer Switches for Fibre Channel traffic and Cisco Nexus Switches for Ethernet traffic. 
## Solution Architecture

<img width="900" height="738" alt="AdaptiveSolutions VVF9 BHE Topology" src="https://github.com/user-attachments/assets/4ed0a1e3-3508-4d72-b961-3a8623b925aa" />

Information on the program is conveyed through white papers and Cisco Validated Designs (CVDs) that are developed as a cooperative effort between the two companies.  

This Github repository covers a sample of playbooks that can be used to provision the UCS Domain Profile and a UCS Server Profile Template through Ansible. The Domain Profile will create the base configuration and port connectivity of the underlying Fabric Interconnects and the Server Profile Template will include the UCS Server Pools and Policies required for the template.  

Some prior steps will need to be performed that are not covered in the Ansible playbooks that will include basic setup with the onboarding of the UCS Fabric Interconnects previously detailed here: https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/hitachi_adaptive_vmware_vsp.html#CiscoIntersightManagedModeConfiguration 

The steps that will need to be followed will be:
- [Intersight Setup](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/hitachi_adaptive_vmware_vsp.html#Intersight_Setup) 
- [Onboarding to Intersight](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/hitachi_adaptive_vmware_vsp.html#OnboardingtoIntersight) 

Running through these steps and the subsequent Ansible playbooks will require an Intersight account at https://intersight.com.

Invocation of the playbooks through Ansible can occur from a number of differing platforms, a Linux system is used as an example within the CVD.  This will require the cisco.intersight ansible-galaxy collection described here:
https://galaxy.ansible.com/ui/repo/published/cisco/intersight/
Installation can be performed running: 
```
ansible-galaxy collection install cisco.intersight
```
If previously installed, an upgrade may be required and accomplished with: 
```
ansible-galaxy collection install cisco.intersight --upgrade
```
With an appropriate platform readied, the repository can be cloned from GitHub with `git clone https://github.com/ucs-compute-solutions/Adaptive_Solutions_IMM_VVF9`

### Cisco Intersight API Access and Configuration

To execute the playbooks against your Intersight account, you need to complete following additional steps of creating an API Key and saving the Secrets_File:

https://community.cisco.com/t5/data-center-and-cloud-documents/intersight-api-overview/ta-p/3651994

The Secrets.txt file (default on download) can be stored as an unencrypted file within the group_vars directory, specified by a relevant vars file.  It can also be pulled into a secrets.yml file within group_vars and encrypted with ansible-vault along with the API Key in this format:
```
vault_intersight_api_key_id: "5########################################################################7"
vault_intersight_api_private_key: |
  -----BEGIN EC PRIVATE KEY-----
  M##############################################################j
  L##############################################################p
  S######################################################B
  -----END EC PRIVATE KEY-----
```
### Cisco UCS Setup Inventory and Variables

To execute the example automation in this repo, update the inventory and variables files to reflect the specifics of your environment. The variables that must be configured to execute the automation are defined in the following locations:

1. Execution does not require an inventory file.
2. Adjustable variables are primarily in the base group_vars directory. 
   - In the repo, these are contained within all.yml, ucs.yml, and secrets.yml
   - All UCS pools, policies, and policies created will use a user specified prefix set by the ucs.yml file  (for e.g. IAC) in the name
   - All Intersight driven configurations will use the tag: `configmode Intersight-Ansible` 

### Cisco UCS Playbook Execution Sequence

**NOTE:** If secrets.yml has been encrypted with ansible-vault, include `--ask-vault-pass` in the invocation.

1.  Create the UCS Domain Profile (base configuration of FI and ports): `ansible-playbook ./Setup_UCS_Domain_Profile.yml`
2.	Create the UCS Server Profile Pools (MAC,WWNN,WWPN,UUID,IP): `ansible-playbook ./Setup_IMM_Pools.yml` 
3.	Create the UCS Server Profile Policies: `ansible-playbook ./Setup_IMM_Server_Policies.yml`
4.	Create the UCS Server Profile Template: `ansible-playbook ./Setup_IMM_Server_Profile_Templates.yml`

### Cisco UCS Post Configuration Tasks

Execution of the playbooks in these repositories set up a Server Profile Template in Intersight. After successfully executing the playbooks, one or more server profiles can easily derived and attached to the compute node from Intersight dashboard. 

## Hitachi VSP One Block Storage Modules for Red Hat Ansible

The following steps must be completed prior to the execution of the Hitachi Storage Module Ansible playbooks for configuration of the VSP One Block storage requirements. 

The steps that will need to have been completed include:

- [VSP One Block 24 Installation](https://docs.hitachivantara.com/r/en-us/virtual-storage-platform-one-block/a3-04-0x/mk-23vsp1b008/installing-your-storage-system)
- [Configuration of VSP One Block NVMe/TCP ports](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_hitachi_adaptive_solutions_nvme_tcp.html#NVMeTCPPortsonHitachiVSPOneBlock)
- [Installation of Hitachi VSP One Block Storage Modules for Red Hat Ansible 3.5](https://docs.hitachivantara.com/v/u/en-us/adapters-and-drivers/3.5.x/mk-92adptr149)

Invocation of the playbooks through Ansible can occur from a number of differing platforms, a Linux system is used as an example within the CVD. This will require the cisco.intersight ansible-galaxy collection described here: [https://galaxy.ansible.com/ui/repo/published/cisco/intersight/](https://galaxy.ansible.com/ui/repo/published/cisco/intersight/)

With the prerequisite steps complete, the file VSP_NVMe_TCP_rev2.yml from the repository root directory will need to be copied to: $HOME/.ansible/collections/ansible_collections/hitachivantara/vspone_block/playbooks/vsp_direct

### Hitachi Storage Modules Access and Configuration

The Linux Ansible control server will be used to execute the Adaptive Solutions NVMe/TCP playbooks for storage. Post installation of the Hitachi VSP One Block Storage Modules for Red Hat Ansible 3.5, all Hitachi playbooks are available at the following location: $HOME/.ansible/collections/ansible_collections/hitachivantara/vspone_block/playbooks

### Hitachi Setup Inventory and Variables 
1. Adjustable variables are primarily in the base group_vars directory.
2. All other required variables will be set in the playbook.

### Hitachi Playbook Execution Sequence

NOTE: If ansible_vault_storage_var_yml has been encrypted with ansible-vault, include --ask-vault-pass in the invocation.
1.	In the playbooks directory, update the VSP_NVMe_TCP_rev2.yml file with the required variables.
2.	Invoke the playbook with the following command:
ansible-playbook VSP_NVMe_TCP_rev2.yml

### Hitachi Post Configuration Playbook status

Execution of the playbooks will result in the creation on the VSP One Block of the following elements.
1.  DDP Pool.
2.	NVMe subsystem.
3.	Assigning NVMe/TCP port to the NVMe subsystem.
4.	Adding host NQNs to the NVMe subsystem.
5.	Creation of an ldev.
6.	Assigning an NVMe Namespace ID to the ldev.
7.	Assigning NVMe/TCP port path to the NVMe Namespace ID of the ldev.

Hitachi Virtual Storage Platform One Block High End Storage Modules for Red Hat Ansible
The following steps must be completed prior to the execution of the Hitachi Storage Module Ansible playbooks for
configuration of the Hitachi Virtual Storage Platform One Block High End (VSP One BHE) storage requirements.
The steps that will need to have been completed include:
VSP One Block High End Installation and Configuration 
Installation of Hitachi VSP One Block Storage Modules for Red Hat Ansible 4.7
Invocation of the playbooks through Ansible can occur from a number of differing platforms, a Linux system is used as
an example within the CVD. This will require the cisco.intersight ansible-galaxy collection described here: https://galaxy.ansible.com/ui/repo/published/cisco/intersight/
With the prerequisite steps complete, copy the yml files listed below from the repository root directory to: $HOME/.ansible/collections/ansible_collections/hitachivantara/vspone_block/playbooks/vsp_direct:
bhe_day0_expressd_interactive.yml
bhe_day0_vsp_one_expressd_prompt_check.yml
bhe_day0_vsp_one_create_boot_ldevs.yml
bhe_day0_vsp_one_create_parity_pool.yml
bhe_day0_vsp_one_create_shared_ldev.yml
bhe_day0_vsp_one_hg_add_luns_to_hg.yml
bhe_day0_vsp_one_hg_rename_wwn.yml
bhe_day0_vsp_one_port.yml
bhe_day0_vsp_one_server_multiple_hbas.yml

Hitachi Storage Modules Access and Configuration
The Linux Ansible control server will be used to execute the Adaptive Solutions BHE playbooks for storage.
Post installation of the Hitachi VSP One Block Storage Modules for Red Hat Ansible 4.7, all Hitachi playbooks
are available at the following location: $HOME/.ansible/collections/ansible_collections/hitachivantara/vspone_block/playbooks

Hitachi Storage Module Usage
Playbook "bhe_day0_vsp_one_expressd_prompt_check.yml" does not require modification since it is only
used to validate an IP address.
All playbooks have been modified to match the variable values used in the CVD Deployment Guide.
Modifying the playbooks to deviate from the default CVD Deployment Guide variable values must be
completed in the individual playbooks.
Playbooks are interdependent. For example, LDEV ID values must be used consistently for the CVD Deployment.
Playbook "bhe_day0_expressd_interactive.yml" is the master playbook invoking other imported playbooks.
Playbook "bhe_day0_expressd_interactive.yml" will be used to enter the Hitachi VSP One BHE userid and password
for the imported playbooks
Update "ansible_vault_vars" with the VSP One BHE userid id and password to execute imported playbooks individually.
.ansible/collections/ansible_collections/hitachivantara/vspone_block/playbooks/ansible_vault_vars

Hitachi Playbook Execution Prerequisites
In the CVD Deployment Guide two Raid6 parity groups have been created.
Using Hitachi VSP One BHE Administrator CCI - verify the available parity groups with the following command:***********
- Update the parity group ids used in bhe_day0_vsp_one_create_parity_pool.yml

Hitachi Playbook Execution Sequence
NOTE: If ansible_vault_storage_var_yml has been encrypted with ansible-vault, include --ask-vault-pass in the invocation.
If deviating from the default CVD Deployment configuration, in the playbooks directory, update bhe_day0_expressd_interactive.yml
with your customization values.
Invoke the interactive playbook with the following command: ansible-playbook bhe_day0_expressd_interactive.yml.
Default variable values can be used. Prompts are available to override some variables.
Hitachi Post Configuration Playbook status
Execution of the playbooks will result in the configuration of the following elements.
FC-SCSI Port configuration.
DDP Pools
Register servers and create array port paths.
Boot LDEVs.
Shared LDEVS.
Attach LDEVs to servers.
Attach nicknames to server HBAs to identify their fabric association.


## Hitachi Cisco ICO custom workflows for Hitachi Virtual Storage Platform(VSP) for NVMe-oF Configuration

The following steps must be completed prior to the execution of the Hitachi ICO custom workflows for configuring Hitachi VSP NVMe-oF storage.

- [VSP One Block 24 Installation](https://docs.hitachivantara.com/r/en-us/virtual-storage-platform-one-block/a3-04-0x/mk-23vsp1b008/installing-your-storage-system)
- [Configuration of VSP One Block NVMe/TCP ports](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/ucs_hitachi_adaptive_solutions_nvme_tcp.html#NVMeTCPPortsonHitachiVSPOneBlock)
- [Cisco  ICO onboarding of Hitachi VSP OneBlock]( https://www.hitachivantara.com/en-us/pdf/architecture-guide/integrating-virtual-storage-platform-with-cisco-intersight.pdf)

To deploy these custom workflows, end users must download the respective Github workflows contained in “Export_Workflow_Hitachi_VSP_NVMe_OF_Sequence.json”  to their Cisco Intersight account and import the via the ICO Import Workflow function. For information on how to import a workflow into a Cisco Intersight instance,  refer to the following [Documentation](https://docs.hitachivantara.com/v/u/en-us/application-optimized-solutions/mk-sl-280) 

### Hitachi ICO Workflows Execution Sequence

The following steps must be executed once custom ICO workflow has been imported:
1. Select Hitachi_VSP_NVMe_OF_Sequence.
2. Click Execute.
3. Define the following inputs:
   a. Select Storage Device.
   b. Define Pool ID.
   c. Define Volume Label.
   d. Define data reduction mode.
   e. Define LDEV size and unit.
   f. Define NVM Subsystem ID
   g. Define NVM Subsystem Name
   h. Define Host Mode
   i. Check the selection to enable Namespace security settings
   j. Define VSP Port ID
   k. Define Host NQN.
   l. Define Namespace ID

### Hitachi Post-workflow status

Execution of the workflow will result in the creation on the VSP One Block of the following elements.
1.	Creating storage NVM subsystem ID/Name and configuring host mode settings. 
2.	Registering a storage array port to the newly created NVM subsystem ID.
3.	Adding host NQNs to the newly created NVM subsystem ID.
4.	Creating a storage volume with optional capacity savings options. 
5.	Creating storage volume namespace ID.
6.	Creating namespace ID path settings.
