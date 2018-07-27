The e-mission server stack can be configured in many ways, depending on the duration of data collection, the size of the sample, and the amount of background processing required.

The single server installation (https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production) is best for small-medium scale data collection (~ 2000 person hours of data), without any ongoing analysis. For any larger scale data collection (~ 10,000 person hours of data), you want a more sophisticated, multi-tier architecture. While your final architecture will depend on your exact requirements and available resources, this document outlines the architecture and setup that I use for the reference e-mission private data server and the e-mission open data server.

## Overview ##

### Servers and purpose ###

The reference architecture has 4 servers:
- one database server (memory optimized) with lots of storage
- three regular servers (general-purpose) with minimal storage (~ 10 GB for code and ~ 10 GB for logs)
    - webapp: runs the webserver, handles communication to/from the user
    - analysis: periodically runs the pipeline for the processing 
    - notebook: notebooks with read-only access to the database for exploratory analysis

### Networking and security ###

The architecture uses a VPC to regulate traffic to the server components. The database server is in a separate subnet from the three regular servers. The regular servers are in a public subnet where access to/from the wider internet is through an internet gateway. The database server cannot be accessed from the wider internet. It can retrieve external data (e.g. for patches) from the internet using a NAT gateway for ipv4 and an egress-only internet gateway for ipv6.

In addition, security groups enforce that the database can receive connections to the mongodb port only from the regular servers. All servers (database and regular) can only make outgoing HTTP and HTTPS connections.

## Provisioning ##

The VPC with all servers and networking setup can be created on AWS using CloudFormation (https://aws.amazon.com/cloudformation/). The template is available at
- https://s3.amazonaws.com/emission-cloudformation-us-east-1/emission-aws-cloud-formation-template.json for direct use
- https://github.com/e-mission/e-mission-server/blob/master/setup/aws-cloud-formation.json for a checked-in version

It will create resources whose names start with the prefix that you specify when you deploy the CloudFormation stack - e.g. if you use the prefix `cci`, your servers will be `cci-webapp`, `cci-analysis`, `cci-notebooks`...

#### Things to watch for ####

Since CloudFormation has very limited support for ipv6 as of the end of 2017, the default provisioning primarily supports ipv4.
https://github.com/e-mission/e-mission-server/issues/530#issuecomment-354061649

**Optional**: So if you want to optionally future-proof your setup and enable ipv6, you need to do so manually. Fortunately, the related steps are pretty straightforward. The provisioned routing tables and security groups do support ipv6 already - you just need to associate the addresses with the instances.

1. Enable it in the VPC
    1. Log in to the VPC service (Services dropdown -> VPC)
    1. Select your VPC -> Actions -> Edit CIDRs
    1. Add IPv6 CIDR (AWS will auto assign this)
1. Enable it in the subnets. For both public and private subnets
    1. Select the subnet
    1. Subnet Actions -> Edit IPv6 CIDRs
    1. Add IPv6 CIDR
1. Enable it in the instances.
    1. Log in to the EC2 instance (Services dropdown -> EC2)
    1. For each instance
        1. Select the instance.
        1. Actions -> Networking -> Manage IP addresses
        1. Under "IPv6 Addresses", "Assign new IP"
        1. Leave address as "Auto-assign" and update ("Yes, Update)
        1. Instance now has an IP. "Cancel" to move to a different instance

## Setup ##
The provisioning script sets up the hardware resources but does not configure the servers. While it is possible to add scripts that would also setup the servers, you may want to use `cryptfs` (https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production#cryptfs-suggested) and putting the cryptfs password as a parameter would defeat the purpose of using cryptfs - we might as well use EBS encryption instead. I might revisit this later.

These are the steps to set up the software on each of the servers:

### database server ###
Use the mongodb EC2 instructions for setting up the database server.
https://docs.mongodb.com/ecosystem/platforms/amazon-ec2/#deploy-mongodb-ec2
The provisioning step should have already setup `data`, `journal` and `log` EB2 volumes for you to mount.
Note that you do not need to install any emission software on the database server. We could theoretically switch this to a managed database service later if we could address the privacy concerns.

#### Things to watch for ####
1. Since the database server does not support any incoming connections from the general internet, to set it up, we need to ssh into it from one of the other servers. This involves:
   1. copying the standard AWS private key to the webserver
      ```
      [laptop] $ scp -i ~/.ssh/private_key.pem ~/.ssh/private_key.pem ubuntu@webapp:~/.ssh/
      ```
   1. ssh to the webapp
      ```
      [laptop] $ ssh -i ~/.ssh/private_key.pem ubuntu@webapp
      ```
   1. generate a new keypair
      ```
      [webapp] $ ssh-keygen -b 2048
      ```
   1. copy the new public key to the database. `<database-private-ip>` is typically `192.168.1.100` since it is the first and only host on the private subnet.
      ```
      [webapp] $ scp -i ~/.ssh/private_key.pem /home/ubuntu/.ssh/id_rsa.pub ec2-user@<database-private-ip>:~/.ssh
      ```
   1. login to the database and add the public key to the `authorized_keys`
      ```
      [webapp] $ ssh -i ~/.ssh/private_key.pem ec2-user@<database-private-ip>
      [database] $ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
      ```
   1. logout from the database and verify that the new key works. Then remove the private key from the webserver.
      ```
      [database] $ exit
      [webapp] $ ssh ec2-user@<database-private-ip>
      [database] $ exit
      [webapp] $ rm ~/.ssh/private_key.pem
      ```
1. The recommended filesystem for the database (from the mongodb instructions) is xfs. The default aws ami that we use for the database server doesn't support xfs. Enable it using.
    ```
    [database] $ sudo yum install xfsprogs
    ...
    Installed:
      xfsprogs.x86_64 0:4.5.0-9.21.amzn1

    Complete!
    ```
1. If you are going to encrypt the filesystems, you need to do so before mounting them (https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production#cryptfs-suggested)
1. You need to bind to the private IP address so that the DB server can be accessed by the web and analysis servers - e.g. in `/etc/mongod.conf`, in addition to setting `dbpath` and `logpath` (https://docs.mongodb.com/ecosystem/platforms/amazon-ec2/#deploy-mongodb-ec2), set
   ```
   bindIp: 127.0.0.1,<private ipv4 IP>,
   ```
1. If you want to use ipv6 to talk to the database server (at least internally), you need to enable ipv6, and listen to an ipv6 address - e.g. in `/etc/mongod.conf`, in addition to setting `dbpath` and `logpath` (https://docs.mongodb.com/ecosystem/platforms/amazon-ec2/#deploy-mongodb-ec2), set

   ```
   net:
      ipv6: true
      port: 27017
      bindIp: 127.0.0.1,<private ipv4 IP>,<private ipv6 IP>
      # Example: Note *no* spaces between elements in the list
      # if space is preset, will get errors like 
      # getaddrinfo(" 192.168.1.100") failed: Name or service not known
      # bindIp: 127.0.0.1,192.168.1.100,2600:1f1f:4545:7f7f1:2424:9191:5656:101e
   ```

### webserver ###
- Setup the `/code` and `/log` volumes in `/etc/fstab`
```
/dev/nvme1n1            /code    ext4   defaults,auto,noatime,exec 0 0
/dev/nvme2n1            /log     ext4   defaults,auto,noatime,noexec 0 0
```
- Mount them
```
$ sudo mkfs.ext4 /dev/nvme1n1
$ sudo mkfs.ext4 /dev/nvme2n1
$ sudo mkdir -p /code
$ sudo mkdir -p /log
$ sudo mount /code
$ sudo mount /log
$ sudo chown -R ubuntu:ubuntu /code
$ sudo chown -R ubuntu:ubuntu /log
```
- Clone the `e-mission-server` repo (https://github.com/e-mission/e-mission-server) to `/code` and set it up using miniconda (https://github.com/e-mission/e-mission-server#python-distribution)
- Configure the port, auth, and other integrations (https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production#configure-the-server)
- Get the javascript packages
- Set the database (`db.conf.json`) to the IP address of the database server
- Start the webserver, preferably with a watchdog (https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production#the-webserver)

Things to watch for:
1. You have to install bower (https://stackoverflow.com/questions/21491996/installing-bower-on-ubuntu)
2. The webapp has outbound requests to 9418 enabled by default to support retrieving bower packages over `git://`. You can also use rewrites instead (https://stackoverflow.com/questions/1722807/how-to-convert-git-urls-to-http-urls) and disable that rule for greater control.
1. If you are running over SSL (please do!), change the inbound rules on the webapp security group from HTTP -> HTTPS.
4. Although the EBS volumes are added to the instance as `/dev/sdf` and `/dev/sdg`, they are visible on the host as `/dev/nvme*n1` (http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/nvme-ebs-volumes.html)
5. If you consistently get a message `sudo: unable to resolve host ip-192-168-0-80` while running the sudo commands, you can add a mapping to `/etc/hosts`
   ```
   192.168.0.80 ip-192-168-0-80
   ```
6. Don't forget to change the log configurations (`conf/log`) to store the logs in `/log` instead of `/var/tmp`

### analysis server ###
- Clone the `e-mission-server` repo (https://github.com/e-mission/e-mission-server).
- Configure the integrations that you need
- Set the database (`db.conf.json`) to the IP address of the database server
- Set up the pipeline cronjobs (https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production#the-analysis-pipeline)

### notebook server ###
You have two main approaches for exploratory analysis on the data collected.
1. *Data to code*: Pull the data to your local development server and run the analyses. This is most useful if you are trying to change the existing code - e.g. fix segmentation.
2. *Code to data*: Run the analyses directly against the data. If you want to use this approach, you want to set up a notebook server with direct access to the database.

If you choose (2), there are two ways to set it up:
1. Use a password-protected server, either shared (http://jupyter-notebook.readthedocs.io/en/stable/public_server.html) or with JupyterHub (https://jupyterhub.readthedocs.io/en/latest/)
1. Use ssh redirect. Assuming you have ssh access to the notebook server, you can use local port forwarding to connect a port on your laptop to the notebook server. to a local port on your laptop and then access `localhost:<local_port>` (https://help.ubuntu.com/community/SSH/OpenSSH/PortForwarding).

    ```
    $ ./e-mission-jupyter.bash notebook --config  ~/.jupyter/jupyter_notebook_config_private.py --notebook-dir /notebooks
    [I 06:09:19.549 NotebookApp] The port 8888 is already in use, trying another port.
    [I 06:09:19.557 NotebookApp] Serving notebooks from local directory: /notebooks
    [I 06:09:19.557 NotebookApp] 0 active kernels
    [I 06:09:19.557 NotebookApp] The Jupyter Notebook is running at: http://127.0.0.1:8889/?token=2de104570c5fab4748cb6a390deaab298c6cf94c9608c494
    [I 06:09:19.557 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
    [W 06:09:19.557 NotebookApp] No web browser found: could not locate runnable browser.
    [C 06:09:19.557 NotebookApp]
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
    http://127.0.0.1:8889/?token=<redacted>
    ```

    ```
    $ ssh -L 8889:127.0.0.1:8889 ubuntu@<IP of notebook server>
    ```
    And then navigate to http://127.0.0.1:8889/?token=<redacted> on your laptop.

#### Things to watch for ####
1. Create a new user to run the notebooks. This will ensure that you don't inadvertently (or in the case of a public server, maliciously) do something stupid and irrevocable. Note that the default ubuntu user has passwordless sudo, so options for messing up are endless.
   1. You probably want to create the user as a system user so that you don't have to remember a new password (https://github.com/e-mission/e-mission-server/issues/530#issuecomment-351896967). You can execute commands as the newly created system user using `su` (https://askubuntu.com/questions/294736/run-a-shell-script-as-another-user-that-has-no-password)
2. You can mount a separate disk just for the notebooks. The default provisioning will create a `prefix-notebook-notebooks` EBS drive for this. You can mount this as `/notebooks`, set the ownership to the newly created user, and start the notebook server with `--notebook-dir=/notebooks`

    ```
    $ sudo -s -u analyst
    $ source activate emission
    $ export HOME=/notebooks
    $ ./e-mission-jupyter.bash notebook --notebook-dir=/notebooks
    ```
3. Since the working directory is now `/notebooks`, the config files are not loaded correctly. You need to edit `emission/core/get_database.py` and change the path to load the database conf to the absolute instead of the relative path. TODO: Make this include an environment variable or a prefix instead.

4. For security on public servers, and for safety on private servers, you may want to ensure that the connection from the notebook server to the database is read-only. This will ensure that analysts don't inadvertently delete anything critical from the server. Instructions for doing this are the section on setting up DB auth.

## Setup DB auth ##

Even if the network only allows connections from certain hosts, we still need to setup authentication for two reasons:
- as a second level layer of defense
- to support different read-write behavior for different connections if running an exploratory ipython notebook server

Steps to do this are:
1. decide admin, read-write and read-only usernames and passwords. You can use https://xkpasswd.net or http://correcthorsebatterystaple.net/ to generate xkcd-compliant passwords, or something like https://www.random.org/passwords/ for more token-like passwords.
1. edit the settings at the top of the `setup/db_auth.py`. Run the script and create users.
https://github.com/e-mission/e-mission-server/issues/530#issuecomment-351578786
1. Turn on auth on the db server
   1. Use the `--auth` command line option
   1. Edit the `/etc/mongod.conf` file to enable authorization

   ```
   #security:
   security:
     authorization: "enabled"
   ```
1. Change the url in the `conf/storage/db.conf` file from the DB hostname to an URL that embeds the username and password. So for the webapp and analysis servers, which need to write to the database, db.conf would be

```
mongodb://<rw-username>:<rw-passwd>@<<db_host>/admin?authMechanism=SCRAM-SHA-1
```

And for the notebook server, it would be

```
mongodb://<ro-username>:<ro-passwd>@<<db_host>/admin?authMechanism=SCRAM-SHA-1
```
