# Manual installation #

If you don't want to use docker, or you want to contribute to server development, you can also install the server directly. We use a virtual environment for the installation, so it will not mess up your regular python install.

The installation instructions below are generally targeted towards OSX and \*nix shells such as bash. If you want to use Windows, we recomend using a POSIX compliant shell such as [gitbash](https://openhatch.org/missions/windows-setup/install-git-bash), which provides similarly rich commands. If you really want to use the Command Prompt, most commands should work, but you may need to convert `/` -> `\` to make the commands work.


## Install/update: ##
-------------------

### Installation ###
is as simple as cloning the github repository.

- If you do not plan to make changes to the code, clone the master repository.

  ```
  $ git clone https://github.com/e-mission/e-mission-server.git
  ```

- If you might make changes or develop new features, fork (https://help.github.com/articles/fork-a-repo/) and clone your fork.

  ```
  $ git clone https://github.com/<username>/e-mission-server.git
  ```

### Update ###
is as simple as pulling new changes.

- If you are working off the master repository

  ```
  $ git pull origin master
  ```

- If you are working off your fork, you will need to sync your fork with the main repository (https://help.github.com/articles/syncing-a-fork/) and then pull from your fork.

  ```
  $ git pull origin master
  ```

## Dependencies: ##
-------------------

### Database: ###
1. Install [Mongodb](http://www.mongodb.org/), version 3.4
  2. *Windows*: mongodb appears to be installed as a service on Windows devices and it starts automatically on reboot
  3. *OSX*: You want to install homebrew and then use homebrew to install mongodb. Follow these instruction on how to do so ---> (https://docs.mongodb.com/v3.4/tutorial/install-mongodb-on-ubuntu/)
  4. *Ubuntu*: http://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/

2. Start it at the default port

     `$ mongod`

### Python distribution ###
We will use a distribution of python that is optimized for scientific
computing. The [anaconda](https://store.continuum.io/cshop/anaconda/)
distribution is available for a wide variety of platforms and includes the
python scientific computing libraries (numpy/scipy/scikit-learn) along with
native implementations for performance. Using the distribution avoids native
library inconsistencies between versions.

The distribution also includes its own version of pip, and a separate environment
management tool called 'conda'.

The distribution also includes an environment management tool called 'conda'. We will set up a separate `emission`
environment within anaconda to avoid conflicts with other applications.

- Install miniconda

  ```
  $ source setup/setup_conda.sh
  ```
  IMPORTANT: this script works for MacOSX and Ubuntu based Linux distributions, the OS must be specified on the command with one of two choices: ```Linux-x86_64``` or ```MacOSX-x86_64```. The final command would be this ```source setup/setup_conda.sh Linux-x86_64``` or this ```source setup/setup_conda.sh MacOSX-x86_64```.

- Setup the `emission` environment.

  ```
  $ source setup/setup.sh
  ```
  
  - If you want to use an ipython notebook, use `$ source setup/setup_notebook.sh` instead.
  - If you want to use a less optimized version that uses less disk space, use `$ source setup/setup_nomkl.sh` instead. Note that this has not been extensively tested.

- Verify that you are in the right environment - your prompt should start with
  `(emission)` and the `emission` environment should be starred.

  ```
  (emission) ...$ conda env list
  # conda environments:
  #
  aws                      /..../anaconda/envs/aws
  emission              *  /..../anaconda/envs/emission
  firebase                 /..../anaconda/envs/firebase
  py27                     /..../anaconda/envs/py27
  py36                     /..../anaconda/envs/py36
  xbos                     /..../anaconda/envs/xbos
  root                     /..../anaconda
  ```
  
- If you have setup the environment already and just need to switch to it, you can also use `conda activate emission` to switch to the emission environment. To switch out of the emission environment, or to manipulate it in other ways, read the conda documentation on environments https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html

- Remember to re-run the setup script every time you pull from the main repository because the dependencies may have changed.

  ```
  $ source setup/setup.sh
  ```

- When you are done working with e-mission, you can cleanup the environment and then just delete the entire directory.

  ```
  $ source setup/teardown.sh
  $ cd ..
  $ rm -rf e-mission-server
  ```

### Javascript dependencies ###

Note: It is required only if the user needs a  web interface for the server. Otherwise one can do without it as well.

Tip: Run "bower install" instead if you are prompted password for 'https://github.com' after running "bower update".

    $ cd webapp
    $ bower update

## Run ##
---------

IMPORTANT: If you have used the ```setup_conda.sh``` script, you will need to run the command 
  ```
  $HOME/miniconda/etc/profile.d/conda.sh
  ```
  on every new terminal. This is due to the installation being made through a script, which doesn't modify the ```bashrc```                       with the correct path.


1. On OSX, start the database  (Note: mongodb appears to be installed as a service on Windows devices and it starts automatically on reboot). 

        $ mongod
        2018-05-23T11:06:07.576-0700 I CONTROL  [initandlisten] MongoDB starting : pid=60899 port=27017 dbpath=/data/db 64-bit ...
        2018-05-23T11:06:07.576-0700 I CONTROL  [initandlisten] db version v3.6.2
        ...
        2018-05-23T11:06:08.420-0700 I FTDC     [initandlisten] Initializing full-time diagnostic data capture with directory '/data/db/diagnostic.data'

1. **Optional** Copy configuration files. The files in `conf` can be used to customize the app with custom authentication options or enable external features such as place lookup and the game integration. Look at the samples in `conf/*`, copy them over and modify as necessary - e.g.

        $ find conf -name \*.sample
        # For the location -> name reverse lookup. Client will lookup if not populated.
        $ cp conf/net/ext_service/nominatim.json.sample conf/net/ext_service/nominatim.json

1. Start the server

        $ ./e-mission-py.bash emission/net/api/cfc_webapp.py
        storage not configured, falling back to sample, default configuration
        Connecting to database URL localhost
        analysis.debug.conf.json not configured, falling back to sample, default configuration
        Finished configuring logging for <RootLogger root (WARNING)>
        Replaced json_dumps in plugin with the one from bson
        Changing bt.json_loads from <function <lambda> at 0x10f5fdb70> to <function loads at 0x1104b6ae8>
        Running with HTTPS turned OFF - use a reverse proxy on production
        Bottle v0.13-dev server starting up (using CherootServer())...
        Listening on http://0.0.0.0:8080/
        Hit Ctrl-C to quit.

1. Test your connection to the server
  * Using a web browser, go to [http://localhost:8080](http://localhost:8080)
  * Using safari in the iOS emulator, go to [http://localhost:8080](http://localhost:8080)
  * Using chrome in the android emulator:
    * change `server.host` in `conf/net/api/webserver.conf` to 0.0.0.0, and 
    * go to the special IP for the current host in the android emulator - [10.0.2.2](https://developer.android.com/tools/devices/emulator.html#networkaddresses)

