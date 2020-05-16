# Setting up an ISAM development lab
This repository contains instructions to standup an IBM security access manager webseal docker instance.
Trial licence acceptance manually through the LMI but the runtime and reverse proxy configuration is performed using 
the IBM ISAM ansible roles.


## Docker Directory
The docker directory contains the following files
 - **start-isam.sh** , this script runs the docker-compose process that starts the ISAM config and ISAM webseal containers
 - **docker-compose.yaml** , the docker compose file that has the ISAM docker config. This is a cut down version taken from the [IBM ISAM site](https://www.ibm.com/support/knowledgecenter/en/SSPREK_9.0.6/com.ibm.isam.doc/admin/concept/con_docker_compose.html) and has an updated version of 9.0.7.1_IF4. 
 - **.env** , environment specific entries for the docker compose file
 - **clear-down-environment.sh** , stops and removes the ISAM containers and clears down the shared volumes. Please be aware that 
   this will remove any ISAM config you've applied not performed through the ansible playbook.


## Ansible directory
- ***apply-config.sh*** This shell runs the ansible playbook to configure the appliance and co-ordinates container restarts. It depends on the IBM [isam ansible roles](https://github.com/IBM-Security/isam-ansible-roles) and downloads and places the roles in a /tmp directory. 
- ***accept_agreement.json*** JSON for accepting the licence agreement. Not really needed for this as the Trial version requires some
manual configuration but might be useful for non Trial versions of the product.
- ***runtime-config.json*** The configuration needed to use the embedded ldap for the runtime config.

## Getting Started
1. Security Access Manager protects applications , I've used this as sample [application](https://github.com/spring-guides/gs-spring-boot/tree/master/complete) to sit behind the reverse proxy. This runs on localhost on my host machine , not a docker container , on port 8080.

[![Step 1](https://res.cloudinary.com/dnrfrgcar/image/upload/v1589628519/SAM-thumbnail_ptkpgo.png )](http://www.youtube.com/watch?v=chhJCSivNnM "Step 1")

2. Start the ISAM containers . Navigate to the docker directory and run `./start-isam.sh`

[![Step 2](https://res.cloudinary.com/dnrfrgcar/image/upload/v1589635988/Screenshot_2020-05-16_at_14.32.33_ovfczk.png)](https://youtu.be/gIDX8AkmT_A "Step 2")



3. This should start the ISAM configuration appliance [login page ](https://localhost:9443/core/login). The default username is ***admin*** and password is ***admin***. It also starts a webseal container but that remains in an unhealthy state until the configuration container publishes the reverse proxy config to the shared volumes. You'll need to download a Trial licence from IBM and activate it within the appliance. IBM have a good [YouTube video](https://youtu.be/2gmlr8sjkkE) on this but the video below shows the steps needed once you have the licence certificate.
[![Step 3](/images/isam-login.png )](https://youtu.be/DDjX1yKlc7I "Step 3")


4. Once you've completed the initial setup , navigate to the ansible directory. Run the `./apply-config.sh` 
   Using the ISAM ansible roles this step
    - configures a runtime environment with the openldap registry
    - configures a reverse proxy called default
    - adds a junction to our application started in Step 1

[![Step 4](https://res.cloudinary.com/dnrfrgcar/image/upload/v1589637467/Screenshot_2020-05-16_at_14.57.18_hv94bz.png)](https://youtu.be/kne8zdncOsw "Step 4")


5. Navigate to https://localhost/isam-test , you should receive the ISAM login page,  use the sec_master user and credentials to login and it should take you to the Spring boot.  

<iframe width="560" height="315" src="https://www.youtube.com/embed/3xUtH6UcyIA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

 

