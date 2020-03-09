# How to test changes to a plugin locally

## Prerequisites
You should have Git installed on you local machine.

You will also need a local copy of the [e-mission-phone]() project. Follow these [instructions](https://github.com/e-mission/e-mission-phone/blob/master/README.md) to get a copy.

## 1. Fork the plugin repository
Go to the GitHub URL of the plugin you would like to test for example; ` https://github.com/e-mission/cordova-usercache` and click on the  **Fork** button.

![Fork a project](../../assets/test_changes_to_a_plugin/fork-project.png)

**Fork** will create a copy of the repository in your Github account so you can make changes to the project.

## 2. Create a local copy on your computer
Navigate to your own Github copy of the repository and copy the url.

![Fork a project](../../assets/test_changes_to_a_plugin/copy-project-url.png)

Open your terminal or git bash window.

Move to the location on your computer where you want to create a copy of the project.

For example;
```bash
$ cd ~/plugins
```
Run the following command:
```bash
$ git clone https://github.com/<username>/<repository>.git
```
> Substitute your Github username for `<username>` and the plugin repository name for `<repository>`.

You now have a local copy on your computer.

## 3. Add the local copy of your plugin to the e-mission phone project

Run the following command to remove the existing plugin
```bash
$ cordova plugin remove edu.berkeley.eecs.emission.<repository>
```
> Substitute plugin repository name for `<repository>`.

Make changes to the plugin

run `cordova prepare` command

> Any time you make changes to the plugin, remove it and re-add it to the e-mission phone project.