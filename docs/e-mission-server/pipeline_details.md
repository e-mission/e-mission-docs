## Pipeline design and running ##

As we have seen multiple times, the data flow involves non-immutable, read-only
data directly from the user, either through automatic sensing on the phone, or
through user inputs. This immutable data is processed by the pipeline to
generate inferred results. The pipeline state controls the running of the pipeline.

### Execution model ###
The pipeline for a single user is intended to be single-threaded, with the
stages running sequentially. Pipelines for multiple users can run in parallel.

In addition, every pipeline stage should:
- only work on fresh data, for improved performance
- work on all unprocessed data, for correctness

We ensure this by maintaining state for each pipeline stage. The state includes
- *the time at which the stage started running*: This is set when the stage
starts running and unset when the stage completes. It is used to enforce
single threading; if this value is set for a particular, then other instances
of the stage should not run.
- *the timestamp of the last data point processed* Note that this is the
*not* the timestamp at which the stage was last run. Since the data can be
buffered at various stages along the way, data that was collected before the
current time might not have made it to the server yet. On every pass, the stage
processes data in the range (last ts processed, now)

### Troubleshooting ###
If you see the error `curr_state.curr_run_ts = [0-9]*, while processing pipeline`, it means that the stage is marked as still running.
Steps to resolve:
1. Make sure that there isn't any other instance of the pipeline running.
1. Assuming that is true:
    - if you are running the pipeline on a small amount of local data (e.g. to
      reproduce on your desktop), reset the pipeline and re-run
    - if you are running the pipeline on an ongoing production server, reset
      the pipeline to a specific date and re-run. Note that this is a trickier
      operation, so you may run into corner cases. In that case, file an issue, or
      fix it youself and send me a pull request!

Note that deleting the pipeline state entries without fully resetting the pipeline *will not work*. The processed results will not be deleted correctly, so you will end up with two sets of trips and sections, which will then mess up the downstream stages.
