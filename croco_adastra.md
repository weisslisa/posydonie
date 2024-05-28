# Install CROCO on ADASTRA CINES

* Git clone CROCO projet from the Gitlab of Inria (assuming you have an Inria GitLab account)
* First setup ssh keys and proxy :
1) copy id_rsa.pub from ADASTRA on the Inria GitLab account
2) edit the config file on ADASTRA in ``` cd $OWN_HOMEDIR/.ssh/config``` such as : 

```
# Meom computation machine
Host ige
      Hostname ige-ssh.u-ga.fr
      User weissli

# GitHub
Host gitlab_croco
      Hostname gitlab.inria.fr
      User git
      ProxyCommand ssh ige nc %h %p
 ```
if needed see also https://docs.gitlab.com/16.8/ee/user/ssh.html

* Clone the GitLab using the ige bridge and then pass to the branch created for the stochastic developments
```
cd $WORKDIR
mkdir CROCO
git clone gitlab_croco:croco-ocean/croco.git
git clone gitlab_croco:croco-ocean/croco_tools.git

cd croco/
git checkout dev_2024_stoAR
git branch
```
