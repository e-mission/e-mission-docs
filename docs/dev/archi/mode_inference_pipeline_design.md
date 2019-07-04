# Mode Inference Pipeline Design
---

## Design goals ##
1. Separate model building and model application steps
1. Seed model building from old-style (Moves) data
1. Support multiple models in parallel

## Serializing the model ##
Both 1. and 2. imply that we should save the model and re-load it.
We can use either standard `pickle` or `jsonpickle` to convert the model to json (with the caveat that it is custom to a particular version of scikit-learn)

But how should we store these models?
1. Store to disk
2. Store to database

Once we move to user-specific models, we can store them to the database. In fact, if we generate models periodically, we might want to store the history of them in the database. But right now, since we don't allow users to edit/confirm modes, we will start with the seed model built from old-style (Moves) data in the Backup and Section databases. Since we don't generate any more Moves-style data, this model will never change.

So should it be stored on disk?

Another thing to note is that we may want to continue to use the old-style (Moves) data until we build up a critical mass of new data. I just checked, and it is `14104 + 7439 = 21543` entries, which is pretty good. It looks like we can combine random forest models 
https://stackoverflow.com/questions/28489667/combining-random-forest-models-in-scikit-learn
Not sure about other models from scikit-learn.



