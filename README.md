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

## Hitachi Virtual Storage Platform One Block High End Storage Modules for Red Hat Ansible  

The following steps must be completed prior to the execution of the Hitachi Storage Module Ansible sample playbooks for
configuration of the Hitachi Virtual Storage Platform One Block High End (VSP One BHE) CVD.  

The steps that will need to have been completed include:

1. [VSP One Block High End Installation and Configuration ](https://docs.hitachivantara.com/r/en-us/mk-26vsp1b030/latest)   
2. [Installation of Hitachi VSP One Block Storage Modules for Red Hat Ansible 4.7](https://github.com/hitachi-vantara/vspone-block-ansible/tree/pre-release-4.7.0)  
3. Installation of the cisco.intersight ansible-galaxy collection. Refer to the "Cisco and Adaptive Solutions for    Converged Infrastructure IMM configuration with Ansible" section in this READMe.md file.  
4. With the prerequisite steps complete, copy the yml files listed below from the repository root directory to:            $HOME/.ansible/collections/ansible_collections/hitachivantara/vspone_block/playbooks/vsp_direct:

   - bhe_day0_expressd_interactive.yml  
   - bhe_day0_vsp_one_expressd_prompt_check.yml  
   - bhe_day0_vsp_one_create_boot_ldevs.yml  
   - bhe_day0_vsp_one_create_parity_pool.yml  
   - bhe_day0_vsp_one_create_shared_ldev.yml  
   - bhe_day0_vsp_one_hg_add_luns_to_hg.yml  
   - bhe_day0_vsp_one_hg_rename_wwn.yml  
   - bhe_day0_vsp_one_port.yml  
   - bhe_day0_vsp_one_server_multiple_hbas.yml  

### Hitachi Storage Modules Access and Configuration

1. The Linux Ansible control server will be used to execute the Adaptive Solutions BHE playbooks for storage.  
2. Post installation of the Hitachi VSP One Block Storage Modules for Red Hat Ansible 4.7, all Hitachi playbooks
   are available at the following location: $HOME/.ansible/collections/ansible_collections/hitachivantara/vspone_block/playbooks

### Hitachi Storage Module Playbook Usage  
  
1. All playbooks have been modified to match the variable values used in the CVD Deployment Guide.
2. Playbook "bhe_day0_vsp_one_expressd_prompt_check.yml" does not require modification since it is only
   used to validate an IP address.  
4. Modifying the playbooks to deviate from the default CVD Deployment Guide variable values must be
   completed in the individual playbooks.  ADD A LINK HERE FOR THE PUBLISHED URL OF THE DEPL GD.
5. Playbooks are interdependent. For example, LDEV ID values must be used consistently in all the playbooks for the CVD Deployment.  Variable values are not passed between the imported playbooks.
6. Playbook "bhe_day0_expressd_interactive.yml" is the master playbook invoking other imported playbooks.  
7. Playbook "bhe_day0_expressd_interactive.yml" will prompt the user to enter the Hitachi VSP One BHE userid and password for execution of the imported playbooks.  
8. Update "ansible_vault_vars" with the VSP One BHE userid id and password to execute imported playbooks individually.
   .ansible/collections/ansible_collections/hitachivantara/vspone_block/playbooks/ansible_vault_vars  

### Hitachi Playbook Execution Prerequisites

1. During the Hitachi Professional Services installation of the VSP One BHE, several parity groups will have been created.
   Using Hitachi VSP One BHE Administrator CCI - verify the available parity groups with the following command:  
    "raidcom get parity_grp"
2. In the playbook bhe_day0_vsp_one_create_parity_pool.yml update the parity group ids to be used for the deployment.

### Hitachi Playbook Execution Sequence

NOTE: If ansible_vault_storage_var_yml has been encrypted with ansible-vault, include --ask-vault-pass in the invocation. This is only required if executing the imported playbooks individually  

1. If deviating from the default CVD Deployment configuration, in the playbooks directory, update bhe_day0_expressd_interactive.yml
   with your customization values.  
2. Invoke the interactive playbook with the following command: ansible-playbook bhe_day0_expressd_interactive.yml.  
   Default variable values can be used. Prompts are available to override some variables.

### Hitachi Post Configuration Playbook Status

Execution of the playbooks will result in the configuration of the following elements:  
1. FC-SCSI Port configuration.  
2. DDP Pools.   
3. Register servers and create array port paths.  
4. Boot LDEVs.  
5. Shared LDEVS.  
6. Attach LDEVs to servers.  
7. Attach nicknames to server HBAs to identify their fabric association.  


## Hitachi Cisco ICO custom workflows for Hitachi Virtual Storage Platform(VSP) for FC-SCSI Configuration

The following steps must be completed prior to the execution of the Hitachi ICO custom workflows for configuring Hitachi VSP FC-SCSI storage.

1. [VSP One Block High End Installation and Configuration ](https://docs.hitachivantara.com/r/en-us/mk-26vsp1b030/latest)
2. [Configuration of VSP One BHE FC-SCSI Ports](UPDATE WITH THE PUBLISHED CVD Deployment Guide Link)
3. Create of parity groups must have been completed on the array.
4. [Cisco  ICO onboarding of Hitachi VSP One Block](https://www.hitachivantara.com/en-us/pdf/architecture-guide/integrating-virtual-storage-platform-with-cisco-intersight.pdf)

To deploy these custom workflows, end users must download the respective Github workflows contained in “VSI_Hitachi_Intersight_VVF9_Workflows_Day_0.json”  to their Cisco Intersight account and import the via the ICO Import Workflow function. For information on how to import a workflow into a Cisco Intersight instance,  refer to the following [Documentation](https://docs.hitachivantara.com/v/u/en-us/application-optimized-solutions/mk-sl-280) under section "Import Custom Workflows".

In the Github repository also download to your desktop the following file Hitachi_VSI_Provisioning_Worksheet_Formatted_v2.docx to assist you in documenting the values to be used for the ICO workflows.  

For detailed information on executing the workflows refer to the "Cisco and Hitachi Adaptive Solutions VVF 9.0 Deployment Guide for VMWare vSphere Foundation 9.0, Hitachi VSP One Block High End and Cisco UCS X210c M8 Servers".  ADD THE URL HERE.   

Five workflows are available to configure the storage requirements and should be executed in the order listed below to mimic the Adaptive Solutions Deployment Guide:

1. VSI – New Hitachi Volume for Pool Volume with Format.   
2. VSI – New Hitachi Pool.   
3. VSI – Hitachi VSP Host Group and Boot Path Creation to UCS Server.    
4. VSI – New Hitachi LUN ID 1 Cluster.
5. VSI – New Hitachi LUN ID 2 Cluster.
  
### Hitachi ICO Workflow 1 Execution Sequence for "VSI - New Hitachi Volume for Pool Volume with Format"    

#### Purpose  
Automate the creation and Quick Format (QFMT) initialization of BASIC LDEVs that will subsequently be used as pool volumes for a Hitachi Dynamic Provisioning (HDP) Pool.  

#### Prerequisites

1. Target Hitachi VSP storage system must be onboarded and healthy within Cisco Intersight.  
2. Storage parity groups must already exist on the selected storage array. Refer to Deployment Guide section Create Parity Groups and Dynamic Provisioning Pool on VSP One BHE with Administrator Command and Control Interface (CCI).
3. Workflow is configured to mirror the reference VSI deployment architecture.

#### Workflow Execution  
The following steps must be executed once the custom ICO workflow has been imported:

1. Select VSI - New Hitachi Volume for Pool Volumes with format.
2. Click Execute.
3. Define the following inputs in the below Required Workflow Inputs table:    

#### Required Workflow Inputs  
| Parameter |	                      Description |    
| --- | --- |  
| Storage Array | 	                     Target Hitachi VSP storage system where volumes will be created and formatted |    
| Parity Group |	                       Existing parity group used for BASIC volume creation, ex 1-1, 1-2, 1-3 etc..  |   
| Volume Label |	                       Modify if deployment requirements are different from VSI reference architecture, Basic_Pool_Volume is hardcoded. | 
| Data Reduction Mode |	                 Data Reduction Mode has been set to disabled and should not be modified. |
| Data Reduction Shared Volume Enabled | Data Reduction Shared Volume Enabled is disabled and should not be modified. |
| Size |	                               Modify if deployment requirements are different from VSI reference architecture, 2640 is hardcoded. |
| Unit |	                               Modify if deployment requirements are different from VSI reference architecture, Gigabyte (GiB) is hardcoded. |
| Format Type  |	                       Format Type 	Quick Format (QFMT) is predefined and should not be modified. |
| Force Format  |	                       Force Format is disabled and should not be modified. |  

#### Workflow Outputs
1. Creates eight (8) BASIC Pool Volumes with no Data Reduction Mode.  
2. Creates 2,640 GB capacity per LDEV.    
3. Performs Quick Format (QFMT) on all volumes.  

#### Workflow Considerations
1. To mirror the reference VSI deployment, execute this workflow twice, once for each parity group. 
2. Record all generated LDEV IDs from the workflow execution logs.  
3. These LDEV IDs are required for Workflow 2.    
4. Verify successful volume formatting before proceeding.   

### Hitachi ICO Workflow 2 Execution Sequence for "VSI - New Hitachi Pool"    

#### Purpose   
Automate creation of a Hitachi Dynamic Provisioning (HDP) Pool using previously created and formatted BASIC LDEVs.

#### Prerequisites
1. Workflow 1 completed successfully.
2. BASIC LDEVs have been created and formatted.
3. Required LDEV IDs have been documented from Workflow 1.
4. Target storage array is onboarded and manageable through Cisco Intersight.

#### Workflow Execution 
The following steps must be executed once the custom ICO workflow has been imported:
1. Select VSI - New Hitachi Pool. 
2. Click Execute. 
3. Define the following inputs in the below Required Workflow Inputs table.   

#### Required Workflow Inputs  
| Parameter |	                      Description |    
| --- | --- |  
| Storage Array |	                  Target Hitachi VSP storage system where the pool will be created. |   
| Pool ID |                        	Available Pool ID, for greenfield config ID 1 can be defined. | 
| Pool Name | 	                    User-defined pool name. |  
| Pool Type | 	                    HDP pool type has been hardoded and should not be modified.| 
| Pool Volumes | 	                  LDEV IDs created in Workflow 1, select the + icon to include multiple BASIC LDEVs. |    
| Warning Threshold | 	            Pool utilization warning threshold, predefined as 70%. |   
| Depletion Threshold |             Pool utilization depletion threshold predefined as 80%. |   
| Suspend Snapshot (Optional) |   	Snapshot behavior when depletion threshold is reached, default set to false. |  

#### Workflow Outputs
1. Creates a new HDP Pool.  
2. Assigns specified BASIC pool LDEVs to the pool.  
3. Applies warning and depletion thresholds.    
4. Applies optional snapshot suspension settings.

#### Workflow Considerations
1. Pool Type is hardcoded as HDP.   
2. Recommended Warning Threshold is 70%.  
3. Recommended Depletion Threshold is 80%.  
4. Pool ID availability must be verified before execution.  
5. Multiple pool volumes may be added using the "+" option.  
6. Validate pool capacity and health after creation.

### Hitachi ICO Workflow 3 Execution Sequence for "VSI – Hitachi VSP One Block Host Group and Boot Path Creation to UCS Server"    

#### Purpose   
Automate creation of a boot volume, host group, WWPN registration, and LUN ID 0 presentation for a single Cisco UCS server per execution of workflow.   

#### Prerequisites
1. Workflow 2 completed successfully.
2. HDP Pool exists on the selected storage array.   
3. UCS server WWPNs are available and noted for both SAN fabrics.   
4. Target storage array is onboarded and manageable through Cisco Intersight.

#### Workflow Execution  
The following steps must be executed once the custom ICO workflow has been imported: 
1. Select VSI - Hitachi VSP Host Group and Boot Path Creation to UCS Server. 
2. Click Execute.
3. Define the following inputs in the below Required Workflow Inputs Table:  

#### Required Workflow Inputs  
| Parameter |	                           Description |    
| --- | --- |  
| Storage Array |	                       Target VSP One Block storage system which will provide Boot LDEV to UCS Servers |   
| Pool ID |                              Existing HDP Pool used for boot volume creation, pool ID 1 is predefined. | 
| Volume Label |                 	       Boot volume naming convention, Boot_Device_ESXi-00x has been predefined, and users must define x based on server being deployed. |
| Data Reduction Mode |                  Data Reduction Mode has been set to compression_deduplication and should not be modified. |
| Data Reduction Shared Volume Enabled | Data Reduction Shared Volume has been set to enabled and should not be modified. |
| Host Group Name  | 	                   User defined Host Group Name, nomenclature predefined per fabric and users must define based on server being deployed, user must define server number by updating x value per server deployment requirements. | 
| Port ID |                              Port IDs based on Reference Architecture have been predefined.
| Host Group Number  | 	                 Host Group Number 1 has been predefined,  users must iterate subsequent host group numbers based on the server being deployed  |    
| UCS Fabric A & B WWNs |               Server WWPNs for Fabric A & B have been partially predefined using Cisco UCS vHBA identifier 20000025B5XXXXX, the subsequent X’s must be defined based on customer environment. | 
| Host Mode | 	                         Host Mode	Host Mode VMware Extension predefined.| 
| Host Mode Options | 	                 Host Mode Options (HMO) 54, 63, 68, 110 predefined, and host mode VMware Extension predefined.  |   

#### Workflow Outputs
1. Creates Boot LDEV.   
2. Creates 4 Host Groups per UCS Server.   
3. Registers Fabric A WWPNs based on VSP port connections.   
4. Registers Fabric B WWPNs based on VSP port connections.     
5. Applies predefined Host Mode (VMware Extension).   
6. Applies predefined Host Mode Options (54, 63, 68, 110).   
7. Allocates Boot LDEV to the server.   

#### Workflow Considerations
1. Host Mode and Host Mode Options are hardcoded.   
2. Boot volume size is hardcoded at 200 GB.   
3. Host Group number 1 is hardcoded, for N+1 iterations admins must define available Host Group ID based on allocated ports.   
4. Data Reduction Mode is hardcoded as Compression and Deduplication.   
5. DRS Volume is enabled by default. 
6. Validate host group creation and LDEV presentation following workflow completion.
7. This workflow provisions a single UCS server.
8. To replicate the reference VSI deployment:  
   a. Execute the workflow four times.  
   b. One execution of this workflow is required per UCS Server.  
 
### Hitachi ICO Workflow 4 Execution Sequence for "VSI – New Hitachi LUN ID 1 Cluster"   

#### Purpose   

Automate creation and allocation of shared VMFS datastore volumes to all servers within a VMware cluster.   

#### Prerequisites

1. Workflow 3 completed successfully.
2. Host Groups exist on the selected storage array.   
3. SAN connectivity is operational.
4. All server WWPNs have been registered.    
5. Target storage array is onboarded and manageable through Cisco Intersight.
  
#### Required Workflow Inputs  

| Parameter |	                           Description |    
| --- | --- |  
| Storage Array |	                       Target VSP One Block storage system that will provide shared LDEV to UCS Servers |   
| Pool ID      |                         Existing HDP Pool used for datastore allocation, Pool ID 1 is predefined. | 
| Volume Label |                 	       Datastore naming convention, Application_VMFS_Volume is predefined |   
| Data Reduction Mode  | 	               Compression & deduplication and DRS predefined and should not be modified   |
| Data Reduction Shared Volume Enabled | Data Reduction Shared Volume has been set to enabled and should not be modified. |
| Size |                                 Capacity of the VMFS datastore 10TiB is predefined |
| Target Host Groups | 	                 Host Groups that will access the datastore based on Fabric A and B connections respectively into CTL1 and CTL2 ports. Fabric A ports CL1-A and CL2-A and Fabric B ports CL1-B and CL2-B are predefined.   |  
    

#### Workflow Outputs

1. Creates VMFS datastore LDEV(s).   
2. Allocates datastore volume(s) to all specified Host Groups.   
3. Assigns LUN IDs.  
4. Presents datastore volumes across all configured server paths using LUN ID 1 predefined.    

#### Workflow Considerations

1. Verify datastore visibility from all ESXi hosts.   
2. Validate VMware multipathing functionality.   
3. Confirm datastore discovery within vCenter.   
4. Verify consistent presentation across Fabric A and Fabric B.   
5. Ensure adequate pool capacity exists before execution. 
6. For usage of workflow after LUN ID "1" has been provisioned, Storage Vendor LUN Options value must be modified for continued iteration use of this workflow.   
   
#### Workflow Execution  

The following steps must be executed once the custom ICO workflow has been imported: 

1. Select VSI - New Hitachi LUN ID Cluster. 
2. Click Execute.
3. Define the following inputs:  
   a. Select Storage Array          
   b. Enter Target Host Groups

### Hitachi ICO Workflow 5 Execution Sequence for "VSI – New Hitachi LUN ID 2 Cluster"   ********Complete this section*********************

  

