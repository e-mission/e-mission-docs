# Changing text through the GitHub UI #

In general, e-mission UI changes are made by [creating a
fork](https://help.github.com/articles/fork-a-repo/), [installing the
devapp](https://github.com/e-mission/e-mission-devapp#installing), [testing the
changes in the emulator](https://github.com/e-mission/e-mission-phone#updating-the-ui-only), and and if using the emTripLog
base app, [submitting a pull request to the branch for your project](https://help.github.com/articles/about-pull-requests/).

But sometimes, you want to make some quick changes without that overhead. The
most common use case is probably a deployer who hired a programmer to make a
custom UI. After the contract is over, she realizes that she needs to make
minor changes to the text - e.g.

  - to the consent, once the final IRB approval is in place,
  - to prompts or other visual elements, based on feedback from a pilot

This document outlines how she can make the changes herself directly online.
easy as editing a google doc!

## Safe changes ##
I would not recommend this for anything other than:

  - direct text substitutions (e.g. "foo" -> "bar")
  - adding non-visual tags (e.g. adding `alt` tags to images)

Anything else has the potential for unexpected breakage and should really be
tested before deployment.

## Steps for making changes ##

1. Find the file to change
    - If your changes will be on master, you can search directly in github
    - If your changes will be on a branch other than master
      - clone the repo to your local computer
      ```
      $ git clone https://github.com/e-mission/e-mission-phone.git
      ```
      - switch to the branch you want to change
      ```
      $ git checkout --track origin/<your_branch>
      ```
      - search for the text using the standard tools for your computer (e.g. using `grep`, opening in an IDE and searching in the project...)
1. Select your branch in the github UI
![branch selection github ui](../../../assets/overview/easiest_way_to_change_text/branch_selection_github_ui.png)
1. Navigate to the file(s) you found in step 1
![navigate to file and edit](../../../assets/overview/easiest_way_to_change_text/navigate_to_file_and_edit.png)
1. Edit the file(s) directly and fill out the "Propose file change" message at the bottom so I know what you are doing. A message about write access is expected. "You’re editing a file in a project you don’t have write access to. Submitting a change to this file will write it to a new branch in your fork"
1. Submit the pull request with the change
1. See the change in your UI channel
