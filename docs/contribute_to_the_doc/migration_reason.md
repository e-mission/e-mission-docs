## Reasons for migrating all the docs to one repository ##

Before this migration, all docs were in wikis. But that structure had several problems:
- since we had separate repositories for the components that should work together to form an end-to-end system, there was no single unifying location to describe the high level overview
- we had to sometimes duplicate documentation across repositories, which makes it challenging to keep them in sync
- the community cannot submit documentation since only users with write access can update the wiki

In order to address this, we create a separate repository for documentation.
- All the end-to-end stuff can go here.
- The community can contribute by submitting pull requests that I can merge after review.
- The documentation can still be in markdown so that it can be directly edited in github without worrying about the git workflow.
- Later, we can use a static website generator such as DocPad (https://docpad.org), Hugo (https://gohugo.io/) or readthedocs (https://readthedocs.org/) to make a prettier looking site.
