## Assessing the impact of algorithm changes ##

One of the big challenges with making changes to the algorithm is that the unit
tests currently ensure that the algorithm generates identical results. This is
useful from a code perspective (see
https://github.com/e-mission/e-mission-server/pull/761), but it is challenging
if we want to improve the algorithm because now all the unit tests fail.

We need to manually go through the results of the unit tests and verify that
they are correct before we can merge the related changes to master.

This has been hard to do, which is why the `gis-based-mode-detection` branch
has been languishing without being merged to master for two years.

However, I finally have a mechanism to run the two different versions of the
algorithms, visualize the differences, and check in modified ground truth. This
is not the perfect solution for comparing the analysis results, but it is a
reasonable start.

### High level design ###

I've figured out how to run test cases outside the test framework and pass in additional flags. 
We modify the test cases to accept the branch name, and `evaluation` and `persistence` flags:
  - If the "evaluation" flag is set:
      - the test registers the newly created user at the beginning of the test with an "email" = "<branch>_<testname>"
      - the test does not clear the entries for the user when the test is complete
  - if the "persistence" flag is set:
      - the test saves the downloaded ground truth into `/tmp/<branch>_<testname>.json`

### Testing procedure ###

After these modifications, the procedure becomes:
- Run the test cases on the old branch by passing in the branch name, and an "evaluation" flag
- Run the test cases on the new branch by passing in the branch name, and "evaluation" and "persistence" flags
- For each test, connect to the server with two different phones, one to `<oldbranch>_<testname>`, and the other to `<newbranch>_<testname>`
- Ensure that the `<newbranch>_<testname>` results are equal to or better than `<newbranch>_<testname>`
    - If they are, replace the ground truth file in the repo with the one from `/tmp/<branch>_<testname>.json`
    - If they are not, try to tweak until they are, then replace
    - If it is not possible to tweak further, but the tradeoff is still towards the new branch being better, replace
- Once all ground truth files have been replaced, ensure that tests pass, and then push to master

Related PR: https://github.com/e-mission/e-mission-server/pull/762
