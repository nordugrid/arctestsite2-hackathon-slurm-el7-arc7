# WIP
# ARC 7 slurm el7 test-site2
This repository holds the arc.conf file and other relevant configuration files for the ARC SLURM test-site with frontend: arctestcluster2-hackathon-slurm-el7-ce1.cern-test.uiocloud.no

This is a mini-cluster with 1 ARC-CE SLURM frontend, and with 1 compute node. 

The purpose of this site is to have a persistent ARC SLURM site where one can do experimentation and testing. 
    
**Table of contents**

[[_TOC_]] 


# Interacting with the web services

## Read ARC conf file as currently  present on site
View arc.conf via curl or web-broser with: [http://arctestcluster2-hackathon-slurm-el7-ce1.cern-test.uiocloud.no/arcconf](http://arctestcluster2-hackathon-slurm-el7-ce1.cern-test.uiocloud.no/arcconf)

## Read ARC log files
Browse ARC log files via curl or web-browser with: [http://arctestcluster2-hackathon-slurm-el7-ce1.cern-test.uiocloud.no/arclogs](http://arctestcluster2-hackathon-slurm-el7-ce1.cern-test.uiocloud.no/arclogs)


## Check ARC state
View ARC state via curl or web-browser with: [http://arctestcluster2-hackathon-slurm-el7-ce1.cern-test.uiocloud.no/arcstatus](http://arctestcluster2-hackathon-slurm-el7-ce1.cern-test.uiocloud.no/arcstatus)



## Replacing arc.conf or slurm configuration via webhook 

Whenever the arc.conf or the slurm config files in the main branch changes via a merge request, the new arc.conf/slurm config files are pulled onto the cluster frontend (arctestcluster2-hackathon-slurm-el7-ce1) and replaced with the old ones. ARC/slurm is restarted with the new configuration. 

You need to label your merge-requests according to what files you have changed. Only the files corresponding to the labels chosen will be updated upon the merge. 

Once the merge request has been accepted, you will see whether the replacement went ok or not by checking the labels on the original MR. If something went wrong, only Maiken can typically check the webhook to see what is wrong. 
The replacement will fail for instance if the arc.conf validation failed e.g. if the arc.conf has some wrong entries or if something actually went wrong with the webhook service itself. 

### How to test/use
Change the arc.conf or slurm configuration files (slurm.conf slurmdbd.conf or slurmnodes.conf) in the following way 
(assuming you know the git cli)

- Clone the repo
- Checkout a new branch 
- Do the changes to the configuration files, push branch upstream
- Create a merge request following the url link from the push
- Add one label per configuration file you changed. Pick from the pre-defined labels.  
  - conf:arc 
  - conf:slurm
  - conf:slurmdbd
  - conf:slurmnodes
- Once the merge request is merged, the webhook will try to replace the configuration files.
- You will see the result as new labels in the merge request
- You can check the status of ARC following the url request mentioned above. 
- To-do Add service to check the slurm status


### Git detailed example
    git clone https://source.coderefinery.org/nordugrid/arctestsite2-hackathon-slurm-el7-arc7.git
    cd arctestsite2-hackathon-slurm-el7-arc7
    git checkout -b mynewtestbranch

Do changes in e.g. arc.conf

    git add arc.conf
    git commit -m "I did some changes in arc.conf for testing the procedure"
    git push -u origin mynewtestbranch

Then follow the instructions from the push command on how to create a merge request with the url provided. <br>
Add appropriate labels.<br>
Merge the merge-request, or ask someone with access to merge for you if you are not allowed.<br>
Only once the merge-request is merged, will the configuration files be updated.

### What do the labels added by the system mean 

- fetch-conf:<conftype>:failure
  - If fetching the configuration from gitlab failed, then this label is added to the merge request by the webhook.
  - Nothing else will be tried so replacing the configuration has failed. 
- update-conf-<conftype>:success 
  - If configuration (e.g. slurm.conf, slurmdbd.conf, arc.conf etc) was successfully replaced
- reconfig:success
  -  After replacing the config the service is restarted or reconfigured, typically with ´´scontrol reconfig´´ or ´´arcctl service restart -a ´´ - if this succeeds, i.e. config is valid, then success
- reconfig:failure: 
  - reconfig failed, so something is wrong with the configuration you tried updating
- reverted-config-<conftype>:success
  - If reconfig:failure then the system tries to restore to the original configuration. And restart the service. This succeeded if label is present.
- cluster:fix-manually 
  - If reconfig:failure and reverting to original config failed, then this label is added. Cluster needs manually restoring/checking. 








## Read Slurm config files
This should correspond to the latest version of the file here in Gitlab. 

Todo: expose the slurm config files


# For developers of the test-site
The webhook related code can be found at: https://source.coderefinery.org/nordugrid/arc-testsites

#### Configuring of the cluster
The ansible playbook used to configure the cluster is found in [configure_slurm_arctestcluster.yml](https://source.coderefinery.org/maikenp/sysadmin/-/blob/master/configure/configure_slurm_arctestcluster.yml). In that repo you will also find the ansible roles that are used. 

### Slurm configuration
The frontend exports its /etc/slurm folder to the compute-node. 

The slurm configuration files are:

 - slurm.conf
 - slurmdbd.conf
 - slurmnodes.conf



