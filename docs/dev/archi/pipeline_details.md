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

### Resetting the pipeline ###
If your pipeline state is messed up in any way, you can just reset the pipeline. The raw data is still present, so you can re-run the pipeline again. Re-running the pipeline after a reset should generate the same results, although it may take a LOOOONG time depending on the data that you have.
  - if you are running the pipeline on a small amount of local data (e.g. to
      reproduce on your desktop), you can reset the entire pipeline (`$ ./e-mission-py.bash bin/reset_pipeline.py -all`) and re-run
  - if you ran the pipeline for a single user, you can reset the pipeline for that user (`$ ./e-mission-py.bash bin/reset_pipeline.py [-u|-e]`)
  - if you are running the pipeline on an ongoing production server, reset
      the pipeline to a specific date and re-run (`$ ./e-mission-py.sh bin/reset_pipeline.py -h` for the format). Note that this is a trickier
      operation, so you may run into corner cases. In that case, file an issue, or
      fix it youself and send me a pull request!

Note that deleting the pipeline state entries without fully resetting the pipeline *will not work*. The processed results will not be deleted correctly, so you will end up with two sets of trips and sections, which will then mess up the downstream stages.

### Troubleshooting ###
#### `curr_run_ts` = ...., while processing pipeline ####
If you see the error `curr_state.curr_run_ts = [0-9]*, while processing pipeline`, it means that the stage is marked as still running.
Steps to resolve:
1. Make sure that there isn't any other instance of the pipeline running (e.g. something like `$ ps -aef | grep intake`)
1. Assuming that is true, [reset the pipeline](#resetting_the_pipeline)   
You can reset the pipeline only back to the day before the error occurred (`-d` option). Also, before the pipeline to run the pipeline, you have to check that for the user(s) for which you had the error, the curr_ is not null in mongodb (Example: `db.getCollection('Stage_pipeline_state').find({"user_id": LUUID("3a2299d0-605a-4d92-bef8-1058d145d301")})`) or you will have to reset the `curr_run_ts` to `null` for that user and for the pipeline stage where it is not `null`.

#### No errors, but data not processed ####
Make sure that the pipeline state is such that your new data is actually considered fresh. Note that on every run, we only read data after the last timestamp processed. Each pipeline stage will print the time range that is considering, and how much fresh data matched it.

For example, the following logs indicate that when this stage was run, it starts from the beginning `None` and finds 617 locations to process. At the end, we have finished processing until 2018-10-08T02:58:11.356361.

```
2018-10-17 20:15:32,290:INFO:140735562040192:**********UUID 2b267782-a822-4b4b-954b-7f5b216c7eef: segmenting into trips**********
2018-10-17 20:15:32,291:INFO:140735562040192:For stage PipelineStages.TRIP_SEGMENTATION, start_ts is None
2018-10-17 20:15:32,296:DEBUG:140735562040192:curr_query = {'user_id': UUID('2b267782-a822-4b4b-954b-7f5b216c7eef'), '$or': [{'metadata.key': 'background/filtered_location'}], 'metadata.write_ts': {'$lte': 1539832527.2915819}}, sort_key = metadata.write_ts
2018-10-17 20:15:32,296:DEBUG:140735562040192:orig_ts_db_keys = ['background/filtered_location'], analysis_ts_db_keys = []
2018-10-17 20:15:32,583:DEBUG:140735562040192:finished querying values for ['background/filtered_location'], count = 173
2018-10-17 20:15:32,671:DEBUG:140735562040192:finished querying values for [], count = 0
2018-10-17 20:15:32,677:DEBUG:140735562040192:orig_ts_db_matches = 173, analysis_ts_db_matches = 0
2018-10-17 20:15:32,718:DEBUG:140735562040192:Found 173 results
2018-10-17 20:15:32,726:DEBUG:140735562040192:After de-duping, converted 173 points to 173
2018-10-17 20:15:33,519:INFO:140735562040192:For stage PipelineStages.TRIP_SEGMENTATION, last_ts_processed = 2018-10-08T02:58:11.356361
```

If we re-run the pipeline for the same user and have not received any new data in the interim, we will see that we query from the last processed ts (`2018-10-08T02:58:11.356361`), which returns only 3 points. So there really isn't any new data to process.

```
2018-10-26 15:04:41,426:INFO:140736223740800:**********UUID 2b267782-a822-4b4b-954b-7f5b216c7eef: segmenting into trips**********
2018-10-26 15:04:41,427:INFO:140736223740800:For stage PipelineStages.TRIP_SEGMENTATION, start_ts = 2018-10-08T02:58:11.356361
2018-10-26 15:04:41,432:DEBUG:140736223740800:curr_query = {'invalid': {'$exists': False}, 'user_id': UUID('2b267782-a822-4b4b-954b-7f5b216c7eef'), '$or': [{'metadata.key': 'background/filtered_location'}], 'metadata.write_ts': {'$lte': 1540591476.4273138, '$gte': 1538967491.356361}}, sort_key = metadata.write_ts
2018-10-26 15:04:41,432:DEBUG:140736223740800:orig_ts_db_keys = ['background/filtered_location'], analysis_ts_db_keys = []
2018-10-26 15:04:41,432:DEBUG:140736223740800:finished querying values for ['background/filtered_location']
2018-10-26 15:04:41,432:DEBUG:140736223740800:finished querying values for []
2018-10-26 15:04:41,462:DEBUG:140736223740800:Found 3 results
2018-10-26 15:04:41,470:DEBUG:140736223740800:After de-duping, converted 3 points to 3
```
If this happens, you want to force the pipeline to treat the old data as fresh. Which means that you basically need to [reset the pipeline](#resetting-the-pipeline).

