# You have a branch for your custom client. Now what?
---

Next steps after I have created a branch (e.g. `custom-ui`) for your custom client. The instructions here are very basic and are intended to be pointers to what you can look up further. You should be familiar with git and the standard github workflow, including forks, branches, pull requests and reviews.

- Fork the e-mission-phone repo (e.g. create `https://github.com/username/e-mission-phone.git`)
- Clone your fork
- Create a new branch (potentially tracking the `custom-ui`) - e.g.
    ```
    $ git checkout -b custom-ui -follow --track origin/custom-ui
    OR
    $ git checkout -b add-new-feature
    ```
- Make the changes you want to your branch, (https://github.com/e-mission/e-mission-phone/wiki/Phone-module-structure) potentially using existing branches (e.g. `cci-berkeley`, `interscity` or `opentoall`) as templates:
  - cherry-picking (`git cherry-pick`) the commits or sets of commits you want to use (better), OR
  - downloading patches from individual pull requests (https://stackoverflow.com/questions/7827002/how-to-apply-a-git-patch-when-given-a-pull-number) OR
  - downloading, editing and applying patches from individual commits if the commit history is badly mangled
    ```
    $ git show <commit> > /tmp/change.patch
    $ patch -i /tmp/change.patch
    ```
- Potentially set up your own server (https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production) and change the `www/json/connectionConfig.json` to point to it (https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production#configuring-the-phone-app)
- Commit and push changes to your branch (e.g. `add-new-feature`) on your repo https://github.com/your-username/e-mission-phone.git

   ```
   $ git commit
   $ git push origin add-new-feature
   ```
- Generate pull request into the *`custom-ui`, not `master`* branch)
- I will review for basic sanity, merge and deploy to your channel
- Ask people to deploy from your channel (e.g. https://e-mission.eecs.berkeley.edu/#/client_setup?new_client=custom-ui&clear_usercache=true&clear_local_storage=true)
- ????
- Knowledge!