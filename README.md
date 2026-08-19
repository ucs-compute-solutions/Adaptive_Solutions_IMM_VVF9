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
configuration of the Hitachi Virtual Storage Platform One Block High End (VSP One BHE) storage requirements.  

The steps that will need to have been completed include:

1. [VSP One Block High End Installation and Configuration ](https://docs.hitachivantara.com/r/en-us/mk-26vsp1b030/latest)   
2. [Installation of Hitachi VSP One Block Storage Modules for Red Hat Ansible 4.7](https://github.com/hitachi-vantara/vspone-block-ansible/tree/pre-release-4.7.0)  
3. Completed installation of the cisco.intersight ansible-galaxy collection. Refer to the Cisco and Adaptive Solutions for Converged  
   Infrastructure IMM configuration with Ansible section in this READMe.md file.  
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

### Hitachi Storage Module Usage

1. Playbook "bhe_day0_vsp_one_expressd_prompt_check.yml" does not require modification since it is only
   used to validate an IP address.  
2. All playbooks have been modified to match the variable values used in the CVD Deployment Guide.  
3. Modifying the playbooks to deviate from the default CVD Deployment Guide variable values must be
   completed in the individual playbooks.  
4. Playbooks are interdependent. For example, LDEV ID values must be used consistently for the CVD Deployment.  
5. Playbook "bhe_day0_expressd_interactive.yml" is the master playbook invoking other imported playbooks.  
6. Playbook "bhe_day0_expressd_interactive.yml" will be used to enter the Hitachi VSP One BHE userid and password
   for the imported playbooks.  
7. Update "ansible_vault_vars" with the VSP One BHE userid id and password to execute imported playbooks individually.
   .ansible/collections/ansible_collections/hitachivantara/vspone_block/playbooks/ansible_vault_vars  

### Hitachi Playbook Execution Prerequisites

1. During the Hitachi Professional Services installation of the VSP One BHE, several parity groups will have been created.
   Using Hitachi VSP One BHE Administrator CCI - verify the available parity groups with the following command:  
    "raidcom get parity_grp"
2. In the playbook bhe_day0_vsp_one_create_parity_pool.yml update the parity group ids to be used for the deployment.

### Hitachi Playbook Execution Sequence

NOTE: If ansible_vault_storage_var_yml has been encrypted with ansible-vault, include --ask-vault-pass in the invocation.
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
3. [Cisco  ICO onboarding of Hitachi VSP One Block](https://www.hitachivantara.com/en-us/pdf/architecture-guide/integrating-virtual-storage-platform-with-cisco-intersight.pdf)

To deploy these custom workflows, end users must download the respective Github workflows contained in “VSI_workflows.json”  to their Cisco Intersight account and import the via the ICO Import Workflow function. For information on how to import a workflow into a Cisco Intersight instance,  refer to the following [Documentation](https://docs.hitachivantara.com/v/u/en-us/application-optimized-solutions/mk-sl-280) 

For detailed information on executing the workflows refer to the Cisco and Hitachi Adaptive Solutions VVF 9.0 Deployment Guide for VMWare vSphere Foundation 9.0, Hitachi VSP One Block and   
Cisco UCS X210c M8 Servers.  ADD THE URL HERE. 

Four workflows are available to configure the storage requirements and should be executed in the order listed below:

1. VSI – New Hitachi Volume for Pool Volume with Format.   
2. VSI – New Hitachi Pool.   
3. VSI – Hitachi VSP Host Group and Boot Path Creation to UCS Server.    
4. VSI – New Hitachi LUN ID Cluster.  

### Hitachi ICO Workflow 1 Execution Sequence for VSI - New Hitachi Volume for Pool Volume with Format **  

#### Purpose  
Automate the creation and Quick Format (QFMT) initialization of BASIC LDEVs that will subsequently be used as pool volumes for a Hitachi Dynamic Provisioning (HDP) Pool.  

#### Prerequisites

1. Target Hitachi VSP storage system must be onboarded and healthy within Cisco Intersight.  
2. Storage parity groups must already exist on the selected storage array.  
3. Parity groups must be created using:  
   a. Hitachi Command Control Interface (CCI)/RAID Manager (RAIDCOM).  
   b. Embedded Administrator / Element Manager.  
4. Workflow is configured to mirror the reference VSI deployment architecture.  

#### Required Workflow Inputs  

| Parameter |	                      Description |    
| --- | --- |  
| Storage Array | 	                Target Hitachi VSP storage system where volumes will be created |    
| Parity Group |	                  Existing parity group used for volume creation |    
| Capacity Override (Optional) |	  Modify if deployment requirements differ from VSI reference architecture, 2640Gb is hardcoded. |  

#### Workflow Outputs

1. Creates eight (8) BASIC Pool Volumes with no Data Reduction Mode.  
2. Creates 2,640 GB capacity per LDEV.    
3. Performs Quick Format (QFMT) on all volumes.  

#### Workflow Considerations

1. Record all generated LDEV IDs from the workflow execution logs.  
2. These LDEV IDs are required for Workflow 2.  
3. To mirror the reference VSI deployment, execute this workflow twice, once for each parity group.  
4. Verify successful volume formatting before proceeding.  

#### Workflow Execution  

The following steps must be executed once the custom ICO workflow has been imported:

1. Select VSI - New Hitachi Volume for Pool Volumes with format.
2. Click Execute.
3. Define the following inputs:  
   a. Select Storage Array.  
   b. Select Parity Group.  
   c. Capacity Override (Optional).   

#### Hitachi Post-workflow status

Execution of the workflow will result in the creation on the VSP One BHE of the following elements:
1.	Eight BASIC Pool Volumes with no Data Reduction Mode have been created.
2.	2640 GB Capacity per LDEV has been created.  
3.	Quick Format (QFMT) has been performed on all volumes.

### Hitachi ICO Workflow 2 Execution Sequence for VSI - New Hitachi Pool **  

#### Purpose   

Automate creation of a Hitachi Dynamic Provisioning (HDP) Pool using previously created and formatted BASIC LDEVs.

#### Prerequisites

1. Workflow 1 completed successfully.
2. BASIC LDEVs have been created and formatted.
3. Required LDEV IDs have been documented from Workflow 1.
4. Target storage array is onboarded and manageable through Cisco Intersight.

#### Required Workflow Inputs  

| Parameter |	                      Description |    
| --- | --- |  
| Storage Array |	                  Target Hitachi VSP storage system where the pool will be created |   
| Pool ID |                        	Available HDP Pool ID | 
| Pool Name | 	                    User-defined pool name |  
| Pool Volumes | 	                  LDEV IDs created in Workflow 1 |    
| Warning Threshold | 	            Pool utilization warning threshold, predefined as 70% |   
| Depletion Threshold |             Pool utilization depletion threshold predefined as 80% |   
| Suspend Snapshot (Optional) |   	Snapshot behavior when depletion threshold is reached, default set to false |  

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

#### Workflow Execution 

The following steps must be executed once the custom ICO workflow has been imported:

1. Select VSI - New Hitachi Pool. 
2. Click Execute. 
3. Define the following inputs:  
   a. Select Storage Array  
   b. Enter Pool ID   
   c. Enter Pool Name  
   d. Enter Pool Volumes   
   e. Enter Warning Threshold  
   f. Enter Depletion Threshold  
   g. Enter Suspend Snapshot (Optional)  

#### Hitachi Post-workflow status

Execution of the workflow will result in the creation on the VSP One BHE of the following elements:
1. Boot volume per UCS server. 
2. Host groups per server. 
3. Applied warning message.
4. Applied optional snapshot suspension settings.

### Hitachi ICO Workflow 3 Execution Sequence for VSI – Hitachi VSP Host Group and Boot Path Creation to UCS Server **  

#### Purpose   

Automate creation of a boot volume, host group, WWPN registration, and LUN presentation for a Cisco UCS server.   

#### Prerequisites

1. Workflow 2 completed successfully.
2. HDP Pool exists on the selected storage array.   
3. UCS server WWPNs are available and noted for both SAN fabrics.   
4. Target storage array is onboarded and manageable through Cisco Intersight.

#### Required Workflow Inputs  

| Parameter |	                      Description |    
| --- | --- |  
| Storage Array |	                  Target Hitachi VSP storage system where the pool will be created |   
| Pool ID |                        	Available HDP Pool ID | 
| Port ID |                        	Port IDs based on deployment requirements, predefined based on VSI ports used in this document  | 
| Host Mode Options | 	            Host Mode Options (HMO) 54, 63, 68, 110 predefined, and host mode VMware Extension predefined.  |  
| Host Group Number  | 	            Users must define Host Group Number, initial state predefines 1, must be iterated based on number of servers deployed  |    
| Host Group Name  | 	              %User defined Host Group Name, nomenclature predefined per fabric  |   
| UCS Fabric A WWPNs |              Server WWPNs for Fabric A  |   
| UCS Fabric B WWPNs |   	          Server WWPNs for Fabric B  |    
| Volume Label |                 	  Boot volume naming convention |  

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
   
#### Workflow Execution  

The following steps must be executed once the custom ICO workflow has been imported: 

1. Select VSI - Hitachi VSP Host Group and Boot Path Creation to UCS Server. 
2. Click Execute.
3. Define the following inputs:  
   a. Select Storage Array  
   b. Enter Pool ID   
   c. Enter Port ID    
   d. Enter Host Mode Options (predefined)    
   e. Enter Host Group Number     
   f. Enter UCS Fabric A WWPNs  
   g. Enter UCS Fabric B WWPNs  
   h. Enter Volume Label  
  
#### Hitachi Post-workflow status

Execution of the workflow will result in the creation on the VSP One BHE of the following elements:
1. Boot volume per UCS server. 
2. Host groups per server. 
3. WWPN registration per server. 
4. LUN presentation for a UCS server.
