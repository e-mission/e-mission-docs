# How to Specify Translation Source

When adding a language or testing translation updates, it can be useful to see changes in the devapp. Follow the steps bellow to point your devapp to a specific branch for translations. 

In the GitHub CLI for your e-mission-phone repo, navigate into the locales folder:

`cd locales`

Clear the remote tracking, and set the appropriate upstream and origin repositories:

`git remote rm origin`

`git remote add upstream https://github.com/e-mission/e-mission-translate`

`git remote add origin https://github.com/[your_account]/e-mission-translate`

Check what has been set:

`git remote -v`

Checkout the branch you wish to test

`git checkout --track origin/[branch_to_test]`

Now, the translations you see in the emulator will be pulled from that branch, and your edits in locales can be pushed into your fork/branch of translations.
