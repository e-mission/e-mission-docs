# e-mission-docs
Move out the docs into their own repository.

Our current docs are in wikis, that structure has several problems:
- since we have separate repositories for the components that should work together to form an end-to-end system, there is no single unifying location to describe the high level overview
- we have to sometimes duplicate documentation across repositories, which makes it challenging to keep them in sync
- the community cannot submit documentation since only users with write access can update the wiki

In order to address this, we create a separate repository for documentation.
All the end-to-end stuff can go here.
The community can contribute by submitting pull requests that I can merge after review.
The documentation can still be in markdown so that it can be directly edited in github without worrying about the git workflow.
Later, we can use a static website generator such as DocPad (https://docpad.org), Hugo (https://gohugo.io/) or readthedocs (https://readthedocs.org/) to make a prettier looking site.

Overall structure (simplified and adapted from https://github.com/phonegap/phonegap-docs)

```
 e-mission-docs/
 |
 |__ assets/        # Images, etc
 |
 |__ docs/          # The documentation
 |   |
 |   |__ overview/    # overview, end to end documentation that stiches together various components
 |   |
 |   |__ tutorials/ # step by step instructions to get users more familiar with making changes
```
