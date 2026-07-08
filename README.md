# AdaptiveSolutions_IMM_NVMe_TCP_M8
Ansible repository for the Adaptive Solutions Conveged Infrastructure. This release is focused on a Virtual Server Infrastructure hosted with Cisco UCS X215c M8 servers hosted with a Cisco UCS X-Series Direct Fabric Interconnect and the Hitachi VSP One Block. The Ansible playbooks shown are for a sub-set of a buildout covering the UCS Server Profile Template creation through UCS IMM interacting with Intersight.

# Cisco and Hitachi Adaptive Solutions for Converged Infrastructure IMM configuration with Ansible

The Adaptive Solutions partnership between Cisco and Hitachi produces converged infrastructure based validated reference architectures.  These architectures feature Cisco UCS servers and Hitachi VSP storage, with connecting infrastructure involving Cisco MDS Multi-layer Switches for Fibre Channel traffic and Cisco Nexus Switches for Ethernet traffic. (MDS not a part of this solution)
## Solution Architecture

![AdaptiveSolutions VSI Topology](https://github.com/rcisaac/AdaptiveSolutions_IMM_NVMe_TCP/blob/2cc8fee8cc5453e6d0e919c375ebabfd185d433c/AdaptiveSolutions%20VSI%20X-Direct%20One%20Block%20Topology.png?raw=true)

Information on the program is conveyed through white papers and Cisco Validated Designs (CVDs) that are developed as a cooperative effort between the two companies.  

This Github repository covers a sample of playbooks that can be used to provision the UCS Domain Profile and a UCS Server Profile Template through Ansible. The Domain Profile will create the base configuration and port connectivity of the underlying Fabric Interconnects and the Server Profile Template will include the UCS Server Pools and Policies required for the template.  

Some prior steps will need to be performed that are not covered in the Ansible playbooks that will include basic setup with the onboarding of the UCS Fabric Interconnects is detailed here: https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/hitachi_adaptive_vmware_vsp.html#CiscoIntersightManagedModeConfiguration 

The steps that will need to be followed will be:
- [Intersight Setup](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/hitachi_adaptive_vmware_vsp.html#Intersight_Setup) 
- [Onboarding to Intersight](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/hitachi_adaptive_vmware_vsp.html#OnboardingtoIntersight) 

Running through these steps and the subsequent Ansible playbooks will require an Intersight account at https://intersight.com.

Invocation of the playbooks through Ansible can occur from a number of differing platforms, a Linux system is used as an example within the CVD.  This will require the cisco.intersight ansible-galaxy collection described here:
https://galaxy.ansible.com/ui/repo/published/cisco/intersight/

With an appropriate platform readied, the repository can be cloned from GitHub with `git clone https://github.com/ucs-compute-solutions/Adaptive_Solutions_IMM_VVF9`

### Intersight API Access and Configuration

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
### Setup Inventory and Variables

To execute the example automation in this repo, update the inventory and variables files to reflect the specifics of your environment. The variables that must be configured to execute the automation are defined in the following locations:

1. Execution does not require an inventory file.
2. Adjustable variables are primarily in the base group_vars directory. 
   - In the repo, these are contained within all.yml, ucs.yml, and secrets.yml
   - All UCS pools, policies, and policies created will use a user specified prefix set by the ucs.yml file  (for e.g. IAC) in the name
   - All Intersight driven configurations will use the tag: `configmode Intersight-Ansible` 


### Playbook Execution Sequence

**NOTE:** If secrets.yml has been encrypted with ansible-vault, include `--ask-vault-pass` in the invocation.

1.	Create the UCS Server Profile Pools (MAC,WWNN,WWPN,UUID,IP): `ansible-playbook ./Setup_IMM_Pools.yml` 
2.	Create the UCS Server Profile Policies: `ansible-playbook ./Setup_IMM_Server_Policies.yml`
3.	Create the UCS Server Profile Template: `ansible-playbook ./Setup_IMM_Server_Profile_Templates.yml`

### Post Configuration Tasks

Execution of the playbooks in these repositories set up a Server Profile Template in Intersight. After successfully executing the playbooks, one or more server profiles can easily derived and attached to the compute node from Intersight dashboard. 


