# DeployOpenShiftOnAWS
Deploy an IPI OpenShift cluster on Amazon Web Services by using a host machine.

The following guide shows how to deploy an IPI OpenShift cluster on AWS. The first step is use a host machine, in this case is a RHEL 7 machine, it's needed to install the following tools:

- Install **bash-completion** **tmux** **vim** **python3** **bind-utils** **wget** **unzip** that are going to be used in the future:
  ~~~
   $ yum install -y bash-completion tmux vim python3 bind-utils wget unzip
   
   Loaded plugins: product-id, search-disabled-repos, subscription-manager
   rhel-7-server-extras-rpms                                                                                                                                                                   | 2.0 kB  00:00:00  
   
   ...
   Complete!
  ~~~
  
- Install the AWS cli:
  ~~~
  $ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  ~~~
  
- Unzip the ***.zip** file and install it:

  ~~~
    $ unzip awscliv2.zip && 
  ~~~
  
- Install the AWS cli:
   ~~~
  $ sudo ./aws/install
   ~~~

- Now it's needed to configure the cli, if it's not already done, it's possible to create Access key ID and secret access key by following the [User guide for version 2 on AWS documentation](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-creds). **When creating the access key and access key id, please, save this variables in a safe place like keepass because is the only time you'll be able to see the acceess key password.**

- Configure the cli with the values created:
  ~~~
  $ /usr/local/bin/aws configure 
  
  
  AWS Access Key ID [None]: AKIAU*************
  AWS Secret Access Key [None]: D+dRUz/hOvtmBph*************
  Default region name [None]: eu-west-1
  Default output format [None]: 

  ~~~
  
- Once the AWS cli is configured, it's needed to follow the [Installing a cluster quickly on AWS](https://docs.openshift.com/container-platform/4.9/installing/installing_aws/installing-aws-default.html) documentation from Red Hat, as a summary, it's needed to run the following commands:

- Create an ssh key:
  ~~~
   $ ssh-keygen -t rsa -b 4096 -N '' -f ~/.ssh/id_rsaaws
   
   
   Generating public/private rsa key pair.
   Your identification has been saved in /root/.ssh/id_rsaaws.
   Your public key has been saved in /root/.ssh/id_rsaaws.pub.
   The key fingerprint is:
   SHA256:8sYOy************************ root@<machineName>
   The key's randomart image is:
   +---[RSA 4096]----+
   |    .            |
   | .   +           |
   |. . o o          |
   |.  . =           |
   | ********        |
   |. +o % *         |
   |*********        |
   |*********        |
   | ********        |
   +----[SHA256]-----+

  ~~~
  
- Start the ssh-agent:
  ~~~
   $ eval "$(ssh-agent -s)"
   
   Agent pid 11138

  ~~~
  
- Add the SSH private key to the ssh-agent:

  ~~~
  $ ssh-add ~/.ssh/id_rsaaws
  
  Identity added: /root/.ssh/id_rsaaws (/root/.ssh/id_rsaaws)
  ~~~
  
 Now go to the [ Install OpenShift on AWS with installer-provisioned infrastructure Red Hat documentation section](https://console.redhat.com/openshift/install/aws/installer-provisioned) and copy the `pull secret` that will be used in the deployment process. In the host machine, it's possible to create a folder to track the different versions, once inside the folder, it's needed to download the installer, to do so, open a web browser and paste the [Mirror OpenShift clients](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/) and choose the version needed, then a display like the following will appear:

~~~

4.9.9
	Name 	Size 	Modified 	
	
..
	— 	— 	
	
changelog.html 	813 KB 	Wed Nov 24 13:20:15 2021 	
	
changelog.md 	697 KB 	Wed Nov 24 13:20:15 2021 	
	
openshift-client-linux-4.9.9.tar.gz 	47 MB 	Wed Nov 24 13:20:15 2021 	
	
openshift-client-linux.tar.gz 	47 MB 	Wed Nov 24 13:20:15 2021 	
	
openshift-client-mac-4.9.9.tar.gz 	39 MB 	Wed Nov 24 13:20:17 2021 	
	
openshift-client-mac.tar.gz 	39 MB 	Wed Nov 24 13:20:17 2021 	
	
openshift-client-windows-4.9.9.zip 	38 MB 	Wed Nov 24 13:20:18 2021 	
	
openshift-client-windows.zip 	38 MB 	Wed Nov 24 13:20:18 2021 	
	
openshift-install-linux-4.9.9.tar.gz 	99 MB 	Wed Nov 24 13:20:18 2021 	<------------- THIS ONE
	
openshift-install-linux.tar.gz 	99 MB 	Wed Nov 24 13:20:19 2021 	
	
openshift-install-mac-4.9.9.tar.gz 	105 MB 	Wed Nov 24 13:20:20 2021 	
	
openshift-install-mac.tar.gz 	105 MB 	Wed Nov 24 13:20:21 2021 	
	
opm-linux-4.9.9.tar.gz 	23 MB 	Wed Nov 24 13:20:21 2021 	
	
opm-linux.tar.gz 	23 MB 	Wed Nov 24 13:20:21 2021 	
	
opm-mac-4.9.9.tar.gz 	26 MB 	Wed Nov 24 13:20:22 2021 	
	
opm-mac.tar.gz 	26 MB 	Wed Nov 24 13:20:22 2021 	
	
opm-windows-4.9.9.tar.gz 	24 MB 	Wed Nov 24 13:20:22 2021 	
	
opm-windows.tar.gz 	24 MB 	Wed Nov 24 13:20:23 2021 	
	
release.txt 	24 KB 	Wed Nov 24 13:20:23 2021 	
	
sha256sum.txt 	852 bytes 	Wed Nov 24 13:20:23 2021 	
	
sha256sum.txt.gpg 	1 KB 	Wed Nov 24 13:22:03 2021 	
~~~

Once the version is chosen, copy the link location from the <THIS ONE> showed in the above example and **curl** into the folder created on the host:
  
~~~
  $ cd <FolderName> && wget <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.9.9/openshift-install-linux.tar.gz>
~~~
  
- Extract the installation:
  
  ~~~
    $ tar xvf openshift-install-linux.tar.gz
  ~~~
  
- On the same folder, it's possible to deploy the cluster by running the following:
  
  ~~~
   $ ./openshift-install create cluster
  ~~~
  
  
 **To delete the cluster is needed to run the following command in the same folder as it was deployed, doing that will delete every resource on AWS side, if the host machine dies or the access is lost, it will be needed to delete every resource manually on AWS console**
  
  ~~~
  $ ./openshift-install  destroy cluster --log-level=debug
  ~~~
