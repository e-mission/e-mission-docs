## Scalability Improvements ##
This is the most straightforward task, so if you are new to research, you
should consider starting with this. The first task on the task list, in particular, 
is relatively well defined for a research project, although it still has some
ambiguity around the metrics to use.

Current e-mission scalability, at least for a fully functioning server, is poor. Before I
set up the multi-tier system
(https://github.com/e-mission/e-mission-server/issues/530), we were running on
an m3.xlarge server (64-bit, 4 vCPU, 15 GB RAM). At that time, the analysis pipeline for ~ 50 active
users would take more than a day to run. Even worse, the response time for
users when the pipeline was running was pathetic, on the order of minutes
rather than seconds. Users would give up the app because they assumed that it
was not working.

This is pretty surprising, since the pipeline only looks at unprocessed
entries, so should be `O(nActiveUsers)`. The only explanation for this is that
the underlying storage layer is not scaling linearly, and stored data from
inactive users is causing queries, even on only active users, to be slow. It is
true that some of the inactive users had a lot of entries - for example, some
of them were test phones that were reading data at the maximum frequency
possible. However, the total number of users, both active and inactive, was
under 5000, which seems low to have a minutes-long response time.

### Improve current scalability ###
The first goal of this task is to improve the performance of the server so that
projects can run a reasonable study without spending too much money.
I'm going to define a reasonable study as:
- 10,000 people
- location data every 30sec on android/15m on iOS
- 6 months of data collection

I'm going to define a reasonable amount of computing and storage resources as:
- *compute*: `m5.xlarge` instance (4 vCPU, 16 GB)
- *storage*: 10 GB I/O optimized EB2 instance

This means that the compute + storage for such a study should cost ~ $150 for compute + $30 for storage ~ $180 total.
Ideally, we would be able to specify the users + duration supported for a bunch
of discrete instance points - e.g. 
- `m5.large` instance + 1 GB GP2 EB2 instance (~$70 for compute + ~ 10 cents for storage = $70 total)
- ...

### Explore new techniques ###
Once the first task is done, we should explore other techniques for not just
performance but elasticity. For example, can we use serverless (e.g. lambda)
calculations to run the analysis? If so, we technically need only storage, no
dedicated compute resources. Can we offload old raw data into lower cost, low
performance storage to reduce costs?...

We should also work closely with the privacy team to adapt to their proposed
architecture. For example, should we split out the storage into separate
independent units so that they can encrypt them separately? Or should we
continue storing all data into one database, just encrypted with different
keys? Is the storage choice for multiple separate datastores different from the
choice for a single datastore? etc

### Task list ###
1. Create a workload generator. The work generator should consist of three parts:
    - _Synthetic data generator_: There is an existing trip generator
      (https://github.com/e-mission/e-mission-server/tree/master/emission/simulation),
      however it is obsolete. We need to replace it with a generator that creates
      artificial data in the new format.
      - There won't be existing solutions for this since the data is specific
        to e-mission. You can try to see if there is something similar that you
        can adapt, but set a time limit on the search since it is unlikely.
    - _Workload simulator_: A harness that generates API calls at a pre-defined
      rate against the system (e.g. x `get` calls/sec, y `put` calls/sec, z
      `getTimeline` calls/sec) and records the response time.
      - Note that this can be combined with the synthetic data generator - the
        generated data can be inserted into the system using API calls. This
        mode is closer to the operation of a real system.
      - Note that there are probably existing solutions for this. Look around
        first. Don't fall into the Not Invented Here trap.
    - _Server Launcher_: This is optional, but it will probably make your life
      easier, and will make end-users' lives easier as well. Write a script
      that, given a particular deployment configuration, creates an e-mission
      server instance with that configuration, installs the server, configures the
      server properly and launches the cronjobs.
      - There are approximately 1 million solutions for this. Use them. One of
        the current users (in Australia) supposedly worked on a Docker
        configuration for e-mission. You can contact https://github.com/asiripanich to see if you
        can start with that.
1. Run various workloads against various deployment configurations and figure
out how the server scales
1. If the performance is sub-optimal, look at the logs to figure out what is
    slow and fix it.
    - I suspect that the first fix will involve swapping out the database
      server from mongodb to a real timeseries database. Or maybe there is a
      better way to use mongodb to store intermittent timeseries data?
    - This may not solve the scalability issues, in which case you need to
      figure out what the next bottleneck is, and rinse and repeat
1. Once you get to the limits of performance with the current architecture,
    explore other techniques, primarily for elasticity
    - Serverless computations, particularly if they can be combined with secure
      execution, could allow us to simply store user data and spin up
      computation on demand
    - Supporting longer response times for older data would allow us to support
      long-term data collection (decades or more). The standard techniques for
      this is tiered storage; and it has been implemented already, primarily
      for photo retrieval at Facebook/Google. Look for an existing OSS solution
      before rolling your own.

### A note on Not Invented Here ###
Note that for several of these, there are existing solutions within academia.
They will almost certainly not be as polished as commercial solutions, but
please try to use them anyway. Grad students will be excited to have you use
their work, and should be supportive as long as you are not asking them basic
questions (e.g. how do I look at a log file?).

And note that this is a *research* project, not a commercial one. This means
that in addition to getting things done, which you must do, we want to enter
into a dialog about how systems should be built. Using academic projects allows
us to have that conversation with other systems researchers, and to hopefully,
discover underlying principles that can be generalized and reused.
