# Issue Template Form -> Config File PR

## GitHub Actions

The config generation process has now been (at least partially) automated through the use of a GitHub workflow. The following steps will occur:

- partners fill out the issue template "form"
- upon submission of an issue in the `nrel-openpath-deploy-configs` repo, if it is an instance of the form, a worflow is launched to:
  - read the issue
  - convert it into a JSON file, formatted properly
  - submit a pull request or update an existing pull request to create or update the file

If this workflow executes sucessfully, some configs should be ready to merge immidiately.

Others may require additional steps like getting surveys uploaded or creating a notification schedule.

## Guidance before merge

Obvious things to check for before merging is that it is a known deployment, that there are no missing fields, and than any surveys or custom labels have been provided

## Changes to the Configs

As OpenPATH continues to grow, there may be instances where we need to update the configs - this will require updating the automation.

The issue template is stored in `nrel-openpath-deploy-configs/.github/ISSUE_TEMPLATE/add-new-config.yml`. You can edit that file to add fields to the form. My convention has been to provide the `id` of form fields to match the key in the config JSON file.

The bulk of processing happens in `nrel-openpath-deploy-configs/.github/actions/convertIssue/parse-issue-body.js`. Update this file to add the new key/value pair where appropriate.
