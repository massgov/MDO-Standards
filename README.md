# Massachusetts Data Office Standards 
## Version 0.1

### Table of Contents 
1. Tools
    * Compute - CM
    * Storage - KM
    * AWS Management - KM
    * Identity Management - CM
2. Code
    * R - CM
    * Python - KM
    * Directory Structure
    * Unit Tests
    * Code Reviews
    * Dependency Management
    * Documentation
3. Process
    * Writing Style
    * Plotting
    * Presentations
    * Requesting Additional Resources
    * Productivity and Communication
    * Data Storage and Versioning - KM

### Tools 

#### Storage
    
The priorities for the Massachusetts Data Office for data storage are security, internal accessibility, stability and cost. These priorities can shift based on the project duration, staffing and sensitivity but all four are still the main concerns.  

 * Security
        
 Storage should be easy to secure by changing access controls. The data should be protected with modern security features and robust authentication for access. It also will conform to all legal standards that might accompany the data such as FERPA or HIPAA. See below for specifics on authentication and access controls.

 * Internal Accessibility

 All the data used in storage must be easily accessible by members of the data team. This means that all work will be done on the same standard data and any changes will be available to all members of the team as discussed in the data storage section. This access should be as fast as the data size allows and work with multiple analysis platforms. This also covers the ease of starting up a new storage location, which ideally is instantaneous. 

 * Stability

 The data must be stored in a place that is reliably accessible and stable.

 * Cost

 The team should always attempt to minimize costs while still meeting all other requirements for completing the project successfully and quickly. This includes doing due diligence on internal and external options for storage.

##### Primary Storage Options

All storage options will be set up in the format spelled out in data storage section for easy portalbility of code between team members and platforms.

* S3/Amazon RDS/Other Cloud Services

Amazon's S3 product is the preferred storage location for all shared data used by the data team due to its format agnostic storage. It also provides internet access that can be secured with unique keys. The data is secured and connections are encrpyted. If the data is better stored in a database an Amazon (or other brand) RDS instance can be used that purpose with a similar process to the S3 instance.

This is our default storage location for project and a new bucket is created for each project. 

As the team explores other cloud-based options other systems might take precendence. 

* Agency servers

Agencies might not be able to provide data directly and will only give access to their servers. If this is the case we prefer a dedicated directory and shared access credentials so the team can access the same data. This also may require permissions from MassIT to get around the firewall to access the agencies server.

* Local

Data can be locally stored for temporary processes but for any process that will be shared the data used should be backed up in a location where all other team members can access it. Each project bucket will have a personal folder for individual's branched work. 
    
* MassIT servers - 

Certain specialized servers including RServers could be requested from MITC. For MassIT specific projects such as Mass.gov, data is already stored there and the servers can then be treated as an agency server.



##### Storage Security

All access to cloud and server storage will be controlled with public and private keys. These will either be keys set up from agencies or MassIT or credentials supplied by the cloud vendor. Helpful information on setting up an ssh config file: http://www.howtogeek.com/75007/stupid-geek-tricks-use-your-ssh-config-file-to-create-aliases-for-hosts/

S3 security assumes that all buckets are set to only be accessed by holders of a MassIT AWS account that will be verified by credentials stored locally or on each call. More information on this will be added as the security system is tested. 

#### AWS Management

##### Structure

* S3
    
Each S3 bucket will be named with the following convention PROJECT_NAME.data.digital.mass.gov and will be seeded with a permissions file described below and with the file structure specified in data storage. Each bucket will also be tagged with a tag (data-office:project) NEED THIS for billing purposes.

Tags:

Finance - {"owning_project":project_name}

* EC2

There will be shared EC2 instances to run processes and scripts on large files. These instances will be set up with the appropriate packages and libraries installed to run scripts. 

EC2 instances that are supporting dashboards or a web interface will be 

Individuals can also set up their own instances for one-off tests

Over time the team will create or find an AMI with all the tools and libraries pre-installed to satisfy internal needs.

Tags:

Finance - {"owning_project":project_name}
Ownership - {"team_owner":owner_name}
Language - {"language":language} - which libraries have been prepared



##### Security

By default all the data office's work will be done in a single VPC (virtual private cloud). This cloud will allow team members to run various instances within the cloud and have them all talk to each other securely without any access permitted from external servers. This might require a "jumpbox" setup on a small EC2 instance which serves as a intermediary that team members will use to access database instances on the cloud. 

* S3
    
The S3 buckets will all be set up with a common security policy in JSON form that will only allow members of the data team access to read and write from the bucket. These permissions can be changed on a case by base if certain data needs to be shared outside of the team. The policy will be written as follows :{policy:WE NEED TO COME UP WITH A POLICY!}

* EC2

THIS IS SOMETHING WE'LL NEED TO IRON OUT
Each EC2 instance is secured with a specific .pem file. Individual's instances will store their .pem keys locally which collective ones will be stored on an EC2 instance in the VPC that each user will have the .pem that accesses that server and that server only. 



