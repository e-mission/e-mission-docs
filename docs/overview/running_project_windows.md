# How to get the code running on Windows for testing
There are some different steps to follow in order to run the e-mission server and the e-mission phone app in Windows from the command prompt. In this document, we will highlight the steps in the main documentation page (https://github.com/e-mission/e-mission-server#dependencies) where there are different steps to perform.

Before start following the steps above, the first thing you want to do is to create this path in your computer:
```
C:\var\tmp
```
## e-mission server
### Python distribution
After installing and configuring Anaconda, there is the following step:
- *Setup the emission environment*

instead of running the command stated in this step, please run the following commands into your command prompt:
```
conda env update --name emission --file setup/environment36.yml
activate emission
pip install six --upgrade
```
Note that every time you need to run the code, you have to run the command ```activate emission```. Also, note that you must be at the root of the project in order to access the directory setup/ in the first command stated before.

### Development
In the development section, we will change the step 3 "Start the server". Instead of doing that, first, you have to create or update the following environment variable on windows:

Name of the variable: PYTHONPATH
Value of the variable: C:\MyPath\Anaconda3;C:\MyPath\e-mission-server

Note that in the field "value of the variable" there are two paths separated by a semicolon. The first one is the path on your computer to the folder in which you installed Anaconda. The second is the path in your computer where you have the root folder of the server code.

After doing so, you have to copy the e-mission.bat script  into the root of your project and run the following command:
```
e-mission.bat . emission/net/api/cfc_webapp.py
```
Yes, with that dot in the middle...
So, note that every time you see the command ```./e-mission-py.bash``` in the documentation, you need to run instead ```e-mission.bat . ``` (with the dot after ".bat").
## e-mission phone
### Installation
In step 4 of the installation called "Setup the config" you have run following commands:
```
cd bin
node configure_xml_and_json.js "serve"
```
You need to change the property "setup-serve" in the files:
- package.json
- package.cordobabuild.json
- package.serve.json
You have to change it to look like the following:
```
"setup-serve": "node bin/download_settings_controls.js"
```

