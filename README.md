# Azure Site Recovery
Azure Site Recovery (ASR) provides a reliable solution for disaster recovery of virtual machines (VMs) in the cloud or On Prem. With ASR, we can replicate our on-premises or Azure VMs to a secondary Azure region, ensuring business continuity and minimizing downtime in case of a disaster. ASR offers continuous replication, allowing changes made to the primary VMs to be replicated to the secondary VMs in real-time. In the event of an outage or disaster, we can initiate a failover which will switch to the replicated Resources in the secondary region. By implementing Azure Site Recovery for VMs in the cloud or On Premises, we can enhance the resilience of our infrastructure, protect critical workloads, and handle unexpected disruptions.


# Overview

When implementing Azure Site Recovery (ASR) for an Azure VM in the cloud, Microsoft recommends following a four-step process to ensure a successful setup and effective disaster recovery:

1. Enable Replication
2. Run a disaster recovery drill
3. Run failover and Reprotect
4. Run Failback

Following this four-step process helps ensure a comprehensive and tested disaster recovery solution for the Infrastrucutre. It allows you to establish replication, validate recovery readiness, execute failover, reprotect the environment, and successfully perform failback when the primary region is restored, all while maintaining business continuity and minimizing downtime.

The resources utilized for implementing this project include the following:

- Recovery Vault Service
- Azure Site Recovery
- Storage Accounts
- Virtual Machines
- Virtual Network
- Availibilty Set 

# Steps

## Creating the Infrastructure 

- Creating a Virtual Machine called VM1 in the ‘eastus’ region. 
- Created an Availability Set (AS-1) across 3 Fault Domains and 3 Update Domains.

The basic details of the VM are as follows:

![1](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/b4581cf8-2742-40f0-b4b2-af539919a0ac)


- Virtual Network, VNET-1 with a CIDR range of 10.0.0.0/16
- the subnet-1 has a CIDR range of 10.0.1.0/24.


![2](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/fa9e1239-3654-406a-9b51-af00c3093c17)

- To enable the Azure Site Recovery, I need to deploy the Recovery Services Vault services in a region which is not in the East US (as the Primary Site has been deployed in the East US region).

- Created a Recovery Services Vault called ‘ASR’ in the West US region.

![3](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/91988b9f-0231-45f0-9304-f875d5f75aea)

- Here, in the ASR Recovery Services Vault, I will enable the ‘Enable Site Recovery’ Option, after that I will start the first process i.e., Replication Process for the Azure Virtual Machine.

### Step 1 - Enable Replication
- Enabled the replication for the Azure VM in the West US region. This will create the following resources in the West US region for replication.

![4](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/46f3deca-6725-4630-8b05-e0eebb0a11e6)

- The VM-1 has been replicated.

![5](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/7a816868-58f4-4126-a5ae-81e2d2a0e1d1)

![6](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/a6241df9-d2ad-4a51-937e-64e838d1bb62)

- A New Resource group has been automatically created with the Name of region-1-asr.
- It has the Availability Set, VM Manged Disk and the Virtual Network.

![7](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/0e15f6a9-7a57-472e-b95a-207a53bee199)

- In the Vault-RG, there is Cache Storage Account created. This Cache Storage Account will store all the data from the VM-1 disk.

![8](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/41708758-544c-410f-96a5-77a3af384e86)

- The infrastructure view of the replication process of the VM-1.

![9](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/781702f5-fc36-4820-b5a7-e06c1d1150f2)

### Step 2 – Running a Disaster Recovery Drill
- Taking a Test Failover of the VM in the East US to the West US. The Virtual Network will be VNET-1-asr.

![10](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/fa176477-76ef-46d2-a0c2-f374221aad44)

- New VM-1 was created in the West US region and the VM in the East US region has been stopped.

![11](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/85119bc2-8491-4021-9892-aca52a1ce602)

- The Test Failover was completed successfully. Now, failing over the Services to backup.

### Step 3 - Taking the Failover and Reprotecting
- After failover and committing the failover. The services are running from the West US region.

![13](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/2d549313-7bb6-45b9-b13e-5ee3119d6987)

- Re-protecting the site. A cache storage in the Secondary site which will replicate to the managed disk on the Primary disk, this is done so that if any changes are made during the failover, the changes will be replicated on the Cache Storage account which In turn will replicate on the Managed disk on the Primary.

![12](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/2183925d-58ed-4d9b-8f86-861e07658184)

- Cache Storage Account Created in the West US Region as well.

![14](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/585afc38-2b1e-49a6-adbe-b299e138ab7f)

- Managed Disk in the East US region

![15](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/481ee1a4-79d0-497f-a839-407ee635cad5)

- Infrastructure after the Failover and Reprotecting

![17](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/4538dc7d-3625-48d6-8c60-f038dba09a5f)

### STEP 4 – Failback

- After the synchronization, I will take failback to the primary site – East US.

![16](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/8d1b6f44-3159-4b39-813e-0e095ec0d5e3)

- The synchronization is complete, the services is running in the West US region. 

![19](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/3f0ffb97-699d-48f1-88c4-fdba3abe7cf4)

![18](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/1322ea4e-ba4b-4687-bce8-60a2e6f111b2)

- After the Failover is completed, I have committed to the failover. Now, re-protecting the primary site by replicating the Disk Data to the Secondary site. This will ensure any changes made to the primary is replicated in the Secondary as well. 

![20](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/34bf9534-1f9e-4b16-807c-0857a465898c)

- The VM is running in the Primary Site.

![21](https://github.com/harshitmamgain/Azure-Projects/assets/106948902/536380a9-a617-4977-b2fc-3fcbf0f0fa54)


# Conclusion

In conclusion, implementing Azure Site Recovery (ASR) for Azure VMs in the cloud provides a reliable and robust solution for disaster recovery. By following the recommended four-step process of enabling replication, conducting a disaster recovery drill, executing failover and reprotect, and performing failback, organizations can establish a comprehensive and tested disaster recovery strategy. ASR ensures business continuity, minimizes downtime, and enhances infrastructure resilience by replicating VMs to a secondary Azure region



