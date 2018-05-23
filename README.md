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
 |   |__ overview/    # overview, end to end documentation that stiches together various components
 |   |    |__ page1/  # documentation page image directory
 |   |         |__ image1.png  # image in page1
 |   |         |__ image2.png  # image in page1
 |   |         |__ ...         # image in page1
 |   |
 |   |__ tutorials/ # step by step instructions to get users more familiar with making changes 
 |
 |__ docs/          # The documentation
 |   |
 |   |__ overview/    # overview, end to end documentation that stiches together various components
 |   |    |__ page1.md  # documentation pages
 |   |    .... 
 |   |
 |   |__ tutorials/ # step by step instructions to get users more familiar with making changes
```

# Contributing

1. [Create a fork to make your changes](https://guides.github.com/activities/forking/)
1. Create a branch for your change
1. Add documentation pages to `docs`, and supporting images to `assets`. You can do this directly through the github UI by creating or uploading files. Note the structure for `assets`.
1. [Link images to text](https://guides.github.com/features/mastering-markdown/)
1. Submit a pull request
1. Note that image paths will need to be changed after merging, but I will take care of that manually for now.
