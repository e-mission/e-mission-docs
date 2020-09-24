## e-mission workshop: understanding and influencing human travel behavior (hands-on overview) ##

Read these papers for context.

- [TRB paper describing how to use e-mission functionality](https://people.eecs.berkeley.edu/~shankari/emission_trb_2017_paper.pdf)
- [The in-review paper on the e-mission architecture, please do not distribute](https://people.eecs.berkeley.edu/~shankari/em-arch.pdf)

In this session, we will:

- [Set up the e-mission development environment](small_ui_changes/quickstart.md)
- [Download your own data](#download-your-own-data)
- [Explore the data](#explore-the-data)
- [Change the UI](../small_ui_changes/)
- Change some existing analysis
- Integrate (with OSM?)

### Download your own data ###

- Download your data by emailing it to yourself (Profile -> Download JSON dump)
- Save the data (`*.timeline`) locally
- Open the data in a text editor (e.g. vim/sublime)
- Figure out what kinds of data has been collected

### Explore the data ###

- [Load the data](https://github.com/e-mission/e-mission-server#loading-test-data) (say as user `meme`)
- Run the single user pipeline for user `meme`
- Login to the phone app as `meme` and confirm that you can see the data
- Launch the `Timeseries_Sample.ipynb` and set the user to `meme`'s UUID
- Explore your data
- Share one insight with the rest of us

