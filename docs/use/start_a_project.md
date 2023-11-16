# Getting Started with an NREL Hosted Deployment of OpenPATH

The first step is to contact NREL to draw up an agreement, if you have not contacted us yet please reach out to openpath@nrel.gov to get started. We look forward to hearing about how you plan to use OpenPATH!

## Customizing Your Project

### Fill Out the Form

In order to provide you with a deployment suited to your project's specific needs, we use a special JSON file to configure your app. To create that file, please fill out the form: HERE. Please follow all instructions carefully, as this allows the automated workflow to function as intended and your file to be ready faster!

You will be asked to provide information such as the name of your project, the purpose behind it, project-specific translations, and preferences for app configurations like the onboarding survey, trip labeling, and reminder schemes. It might help to gather the information before beginning to fill out the form. 

### View Issue and Workflow

After you fill out the form and click submit, you will see a page with a GitHub Issue containing the information that you input into the form. Please save the link to this page, we may need you to provide it to us. 

If everything went well, you should see a message at the bottom of the page saying that a bot referenced this issue - this means your config file has been created sucessfully! 

To see the generated pull request, click on "Pull requests" in the toolbar at the top of the page and find the one with the same number as the issue you opened in the title. This Pull Request will add the configuration for your project to our repo. If you're curious, you can see the generated file by clicking "Files changed" in the top of the pull request. 

## Approval and Merge

Once the file is ready, a developer will check it over and if everything looks good will merge the new file into the repository.

Please note that any additonal files, like custom label options or surveys, will also need to be submitted before we can get your project completly ready for testing.

## Something Went Wrong

If you do not see a message at the end of your issue form from a bot within a few minutes of clicking submit, something probably went wrong with the automatic file generation process. 

Don't worry! You can edit the issue to fix the issue. The most likely cause is that you did not follow the instructions on the form. To learn more about the problem, click "actions" in the GitHub toolbar at the top of the page, and then click on the one with [Your Project Name], if it has a red X to the left of the name, there was a problem. Click on the Action and look for an idication of the error, this may help you know what to edit in your issue to fix the problem. 

A green check next to the workflow means everything went smoothly, if you still don't see the bot message please contact us with the link to your issue and we will investigate. 

## How do I Edit After Submitting?

To edit your form after you submit, from the issue page that showed up when you clicked submit, click the 3 dots in the right hand corner of the box containing your form, then click edit. 

The box with your form will now be a text editor. Make updates as needed, ensuring that you keep the formatting intact. The rows starting with "###" are labels from the form, do not change them. 

When you're done, click "Update Comment" at the bottom right of the box.

## Additional Files?

If you are choosing a more highly custom route, you might have to provide us with additonal files. Please use the files linked in the descriptions of those fields on the form as examples. Since this is slightly more complex than relying on the default options, there are some manual steps involved. You can submit the files using a GitHub pull request. If you are new to GitHub, this is a good [guide on pull requests ](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request). If you do create pull requests for additional files, it would help us out if you could link them in a comment in your issue. 

### Custom Onboarding Survey

We provide a default onboarding survey, but if your project needs specialized demographic information, you can write your own survey. The best way to do this is to use KoboToolbox to create the survey, then download the files and submit a pull request to add them to our repository.

### Custom Trip Labels

We have a default set of trip mode and purpose labels, but you can also choose to provide your own. Please create a file like the example for a [program](https://github.com/e-mission/nrel-openpath-deploy-configs/blob/main/label_options/example-program-label-options.json) or a [study](https://github.com/e-mission/nrel-openpath-deploy-configs/blob/main/label_options/example-study-label-options.json) and submit it as a pull request. 

Modes are somewhat complex. The `value` is what's used behind the scenes. For the `baseMode`, you must choose from [those in our code base](https://github.com/e-mission/e-mission-phone/blob/922a62b7c2601f195bfe8df54654986135e99b25/www/js/diary/diaryHelper.ts#L20), these control the icon and color associated with the mode. The `met` or `met_equivalent` relates to the "metabolic equivalent of task" for each mode, you can specify a custom value, or choose an equivalent from [WALKING, BICYCLING, UNKNOWN, or IN_VEHICLE]. Make sure you specify all needed translations, failing to do so could result in poor UI in your version of the app. 

### Custom Trip Surveys

If your data-gathering needs extend beyond travel mode and purpose, you can ask participants to fill out a survey for each trip. Like the onboarding survey, it is best if you create this with KoboToolbox and submit the files with a pull request. If you do this, the dashboard screen will not be avaliable to participants in the app, as it relies too heavily on traditional labels to be insightful without them. 

## Custom Reminder Schemes

If you want to customize the schedule on which notificiations are sent to participants to open the app and label their trips, or what those notifications say, please indicate your preferences in the appropriate field on the form and a developer will translate that into the appropriate JSON in your config.