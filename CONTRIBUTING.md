# Contributing to the documentation

1. [Create a fork to make your changes](https://guides.github.com/activities/forking/)
1. Create a branch for your change
1. Add documentation pages to `docs`, and supporting images to `assets`. You can do this directly through the github UI by creating or uploading files. Note the structure for `assets`.
1. [Link images to text](https://guides.github.com/features/mastering-markdown/)
1. Submit a pull request
1. Please use relative paths (e.g. `../../assets/...`) instead of absolute paths (e.g. https://github.com/e-mission/e-mission-docs/assets/...) while including images. This will ensure that they work even after merging and cloning.


## Overall structure ##

This ASCII diagram shows the parallel tree structures for the docs and assets.
It is simplified and adapted from https://github.com/phonegap/phonegap-docs

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

