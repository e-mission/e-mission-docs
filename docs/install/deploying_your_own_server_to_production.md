# Deploying Your Own Server to Production
---

Projects that want to control their own data under protocols different from e-mission, can run their own copy of e-mission. The e-mission server is easy to install on a laptop for local development, and the same steps can be used to install a basic server on *nix for small projects. This page lists the few additional steps for such a deployment. It also provides recommendations for <a href="#suggested-improvements">more complex deployments</a>. All the software is fairly standard, so the published documentation can form the basis of additional customization.

## Basic instructions ##

### Development installation ###
Install the server following the development dependencies (https://github.com/e-mission/e-mission-server#dependencies) and installation instructions (https://github.com/e-mission/e-mission-server#development). Note that:
> deployment is as simple as pulling from the repo to the real server and changing the config files slightly.

```
$ git clone https://github.com/e-mission/e-mission-server.git
```

While following the instructions to start the server, make sure to point to the python binary from the anaconda distribution.

### Upgrade to production ###
- Install NTP (https://help.ubuntu.com/lts/serverguide/NTP.html). If this is
  not done, the server can become too far out of sync with the client, and we
  end up with errors related to expired tokens
- If running on a cloud provider, note that the cloud provider admins by looking at the disk image directly. you can choose to <a href="#cryptfs-suggested">install an encrypted
  filesystem to store the data</a>

### Configure the server ###
All server configuration files are in `conf`. You need to copy the corresponding sample files to copy specific functionality - e.g.

```
$ cp conf/net/ext_service/habitica.json.sample conf/net/ext_service/habitica.json
```

Full list of sample files can be determined dynamically.

```
$ find conf -name \*.sample
conf/analysis/debug.conf.json.sample
conf/storage/db.conf.sample
conf/net/keys.json.sample
conf/net/api/webserver.conf.sample
...
conf/log/webserver.conf.sample
conf/log/intake.conf.sample
```

#### Configuring the webserver ####

By default, the server runs over `http`. But for production systems, you should really use SSL. It's easy to use SSL, with services like https://letsencrypt.org/2014/11/18/announcing-lets-encrypt.html. Just do it now.

We use cheroot to provide SSL support. The SSL certificate and private key should be specified in `conf/net/keys.json`. 
```
$ cp conf/net/keys.json.sample conf/net/keys.json
$ vim conf/net/keys.json
```

For scalability, you can also choose to run <a href=#running-behind-a-reverse-proxyload-balancer>behind a reverse proxy, or <a href="#running-the-database-on-a-different-server">run your database on a different server</a>.

#### Configuring authentication ####
This is configured in two steps.
- The auth method is configured in `conf/net/api/webserver.conf`
- The settings for the auth method are configured in `conf/net/auth/<auth_method>.json`. 

For more details on configuring an auth method, see https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-both/configuring_authentication.md 

#### Configuring push ####
We use silent push notifications on iOS as a backup in case the visit
notifications don't work (there have been some reports of this on iPhone7
devices). So configuring push is a good idea to avoid excessive power drain on
iOS.

Push notifications are configured in `conf/net/ext_service/push.json`.

- If you are deploying a new UI on the existing e-mission app, ask me for the
  auth token.
- If you are deploying your own app, create an ionic account, register your
  app, and obtain an auth token for push.


#### Configuring other services ####
The server has a number of integrations with both internal and external
services - e.g. nominatim, habitica, push notifications, etc. The relevant
configurations are in the `conf/net/int_services` and `conf/net/ext_services`
directories. Since these are integrations, they are not core to the
functionality of the server, and can be configured as desired.

#### Configuring logs ####
The sample log configuration is intended to be reasonable. The one caveat is
that it logs to `/var/tmp` by default which is unencrypted. So if deployed on a
cloud server, the cloud admins can see the logs and try to reconstruct the
data. So to be really careful, the logs should also be set to a location on
`edfs` - e.g. setup

```
$ sudo cryptsetup -y luksFormat /dev/xvdc
$ sudo cryptsetup luksOpen /dev/xvdc xvdc
$ sudo mount /dev/mapper/xvdc /mnt/logs
```

and then change the config to point to `/mnt/logs`, e.g.

```
"filename": "/mnt/logs/emission/intake-errors.log.gz"
```

#### Configuring the phone app ####
The connection settings on the phone are at `www/json/connectionConfig.json`. The sample file (`connectionConfig.production.json.sample`) should be filled in with the URL of the production server and the auth method from [Configuring Authentication](#configuring-authentication)

Note, you should also cutomize your client-app with a custom server: [Create a new custom client](../dev/front/create_a_new_custom_client.md).
Currently, the aggregate related URLs (heatmap, metrics tab) are hardcoded. They need to be changed to your server URL in:

- `www/js/heatmap.js`
- `www/js/metrics.js`

It is also necessary to edit the Content-Security-Policy in `www/index.html` so that connections to your own server URL are allowed.

### Starting processes ###
The server needs three ongoing processes. The instructions here are for *nix systems. I've filled in what I've found for Windows equivalents, but they are untested. Any Windows installers, please correct them as required.

#### The webserver ####
  It would be good to have this set up with a watchdog so that it can
  automatically restart if it goes down (e.g. due to out of memory errors).  I
  use the `supervisord` watchdog (http://supervisord.org/). If you want to use
  the same, the e-mission configuration that I use is

   ```
   [program:emissionpy]
   command=sudo ./e-mission-py.bash emission/net/api/cfc_webapp.py
   directory=/code/e-mission-server
   autostart=true
   autorestart=true
   stderr_logfile=/log/emission/emissionpy.err.log
   stdout_logfile=/log/emission/emissionpy.out.log
   ```
  where `/code/` and `/log/` are separate encrypted filesystems.

  1. Note that supervisord only runs on python 2.7, although e-mission is now on python 3.6. You need to set up a parallel py27 environment to run it. https://github.com/e-mission/e-mission-server/issues/530#issuecomment-351776014
  ```
  $ conda create -n py27 python=2.7
  $ source activate py27
  ```
  2. Setup supervisord and configure it based on the instructions (http://supervisord.org/installing.html) - e.g.
  ```
  $ pip install supervisor
  $ echo_supervisord_conf > ~/supervisord.conf
  ```
  3. Since supervisord will run from the `py27` environment, but we want e-mission to run from the `emission` environment, use an absolute path in the batch file `/code/e-mission-server/e-mission-py.bash` by replacing `python -> /home/ubuntu/miniconda3/envs/emission/bin/python`.
  4. Run supervisord (http://supervisord.org/running.html) - e.g.
  ```
  $ supervisord -c ~/supervisord.conf
  $ ps -aef | grep python
  ubuntu 24344     1  0 04:02 ?        00:00:00 /home/ubuntu/miniconda3/envs/py27/bin/python /home/ubuntu/miniconda3/envs/py27/bin/supervisord -c /home/ubuntu/supervisord.conf
  root   24347 24346 17 04:02 ?        00:00:00 /home/ubuntu/miniconda3/envs/emission/bin/python emission/net/api/cfc_webapp.py
  ```
  5. If your install requires a password for sudo, you may only see one python program after start, and get an error in the  `/log/emission/emissionpy.err.log` file (`sudo: no tty present and no askpass program specified`). You can fix this by [enabling passwordless sudo for your root/admin user](http://jeromejaglale.com/doc/unix/ubuntu_sudo_without_password).

##### Windows #####
The suggestion to replace supervisord is to use honcho or a windows service. Note that there might be tricky things to do with a batch file and polling to actually get watchdog functionality. Alternatively, you can use cygwin and apparently then supervisord just works.
https://stackoverflow.com/questions/7629813/is-there-windows-analog-to-supervisord

#### The analysis pipeline ####

This pipeline segments the trips, segments legs within the trips, smoothes, assigns modes, etc. It is run via a cronjob. You can customize the frequency of the cronjob depending on the load on the server. A typical `cronjob` that runs once a day is

```
@daily cd /mnt/e-mission/e-mission-server && PYTHONPATH=. /home/ubuntu/anaconda/bin/python bin/intake_multiprocess.py 3 >> /mnt/logs/emission/intake.stdinout 2>&1
```

##### Windows #####
The suggestion to replace cron is to use the scheduled tasks from the control panel, or the schtasks cmdlets from Windows powershell.

https://stackoverflow.com/questions/132971/what-is-the-windows-version-of-cron

https://technet.microsoft.com/en-us/library/cc725744(v=ws.11).aspx

You can also install cygwin and get it to work with some effort.

https://stackoverflow.com/questions/707184/how-do-you-run-a-crontab-in-cygwin-on-windows


#### The iOS silent push ####
As described earlier, we use iOS silent push as a backup for trip end
detection. We also use it to trigger periodic syncs, and we allow users to
configure their sync frequencies. So we need to have scripts that generate
pushes for all iOS phones for the supported sync frequencies. This is also done
using `cron`.

```
*/1 * * * * cd /mnt/e-mission/e-mission-server && PYTHONPATH=. /home/ubuntu/anaconda/bin/python bin/push/silent_ios_push.py 60 >> /home/e-mission/silent_ios_push.stdinoutlog 2>&1
*/10 * * * * cd /mnt/e-mission/e-mission-server && PYTHONPATH=. /home/ubuntu/anaconda/bin/python bin/push/silent_ios_push.py 600 >> /home/e-mission/silent_ios_push.stdinoutlog 2>&1
*/30 * * * * cd /mnt/e-mission/e-mission-server && PYTHONPATH=. /home/ubuntu/anaconda/bin/python bin/push/silent_ios_push.py 1800 >> /home/e-mission/silent_ios_push.stdinoutlog 2>&1
@hourly cd /mnt/e-mission/e-mission-server && PYTHONPATH=. /home/ubuntu/anaconda/bin/python bin/push/silent_ios_push.py 3600 >> /home/e-mission/silent_ios_push.stdinoutlog 2>&1
```
##### Windows #####
These are also cronjobs so the same techniques as the previous section (https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production/_edit#windows-1) apply.

## Suggested improvements ##

### cryptfs (suggested) ###
If you are running your server on a cloud provider, and want to ensure that the
cloud provider cannot access your data by looking at the files directly, you need to encrypt your filesystem. Some cloud providers provide encrypted storage solutions such as EBS on AWS, but they do not allow you to specify the encryption key (*NOTE*: This may have recently changed. Need to look up AWS key management services).

In order to have more control, you can use an encrypted filesystem such as cryptfs. Instructions for using cryptfs on ubuntu are at http://sleepyhead.de/howto/?href=cryptpart. You want to ensure that the partitions for data and logs are encrypted - data for obvious reasons, and logs because they print a lot of private information that can potentially be reconstructed.

In order to ensure that a disk is mount encrypted, use the cryptfs instruction to create and open it in encrypted mode. Then you can simply replace the instructions using an unencrypted disk with the instructions for using an encrypted disk. For example, if you were going to use `/dev/foo`, you would first follow the instructions at http://sleepyhead.de/howto/?href=cryptpart to get `/dev/mapper/foo`. Then instead of creating a filesystem on and mounting `/dev/foo`, you would perform the same operations on `/dev/mapper/foo`.

#### Decrypt + mount after reboot ####
Note that if you encrypt the filesystem, you will need to decrypt the
filesystem every time you reboot to allow the OS to continue accessing the data
on it.

```
$ sudo cryptsetup luksOpen /dev/foo foo
$ sudo mount /dev/mapper/foo <mount_point>
```

### Running behind a reverse proxy/load balancer ###

TROUBLESHOOTING : If some encounter cannot access the app and the e-mission web site, setting up a Reverse Proxy (RP) is a solution (see: [troubleshooting server FAQ ](https://github.com/fabmob/e-mission-docs/blob/e-mission-contrib/docs/manage/troubleshooting_server_faq.md)).

Here is [an example Apache httpd virtual host configuration](https://github.com/fabmob/e-mission-docs/blob/master/docs/assets/RP-conf.sample) for an e-mission RP.   
In our case the original port for the bottle.py e-mission web server was 8080.   
We added a reverse proxy on apache2 httpd listening on 8080, and passing the requests to the e-mission server now on port 8081.  
Besides adding a virtual host in the httpd.conf and changing the port number in the e-mission conf/net/api/webserver.conf file to 8081, it is necessary to install the mod_proxy and mod_ssl extension and configure SSL (with letsencryt, e.g.).  

For more explanations see the following links:
https://www.digitalocean.com/community/tutorials/how-to-use-apache-as-a-reverse-proxy-with-mod_proxy-on-ubuntu-16-04   
https://admin-ahead.com/forum/general-linux/configure-to-listen-on-multiple-ports-apache/   
https://httpd.apache.org/docs/2.4/en/vhosts/examples.html   

You can also choose to run the server behind a reverse proxy/load balancer such as `ngnix`. In that case, the actual e-mission server will continue to run over HTTP. However, in order to make the development flow smoother, if the server is running over HTTP as opposed to HTTPS, it has no security. It uses the `dummy-dev` authentication method, which returns the user email as the (dummy) authentication token. This means that anybody who knows a users' email address can download their detailed timeline. This is very bad.

To force authentication, edit `emission/net/api/cfc_webapp.py` and set `skipAuth = False`.

```
818       # not really an important use case now, and it makes people have to change
819       # two values and increases the chance of bugs. So let's key the auth skipping from this as well.
820       skipAuth = True
821       print "Running with HTTPS turned OFF, skipAuth = True"
822
```

### Running the database on a different server ###

You can also choose to run the database on a different server for greater scalability.
In that case, you need to change this line from `emission/core/get_database.py`
from `localhost` to your database server.

```
_current_db = MongoClient('localhost').Stage_database
```

The easiest option is to connect using the DB server hostname/IP - e.g.

```
_current_db = MongoClient('192.168.0.5').Stage_database
```

but you can also use more complex configurations if your DB server is sharded, for example. A full list of the connection options is at http://api.mongodb.com/python/2.7/api/pymongo/mongo_client.html#pymongo.mongo_client.MongoClient

Make sure to set up a NAT and put your database server into it to reduce the
risk of unauthorized access.

TODO: Use a config setting instead of requiring code edits
