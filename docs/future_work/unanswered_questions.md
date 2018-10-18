## Privacy ##
All papers assume that everybody has data for every time period. Is that true for e-mission?
- can't you just assume that people have a value, but it is zero?
  - no because what about averages? If you assume 0 values for people who are not participating, averages will be off by a lot
    - for max and min, may be an issue for all neg/pos values
  - user doesn't respond or responds with "no data"
    - violates aggregator obliviousness
     - is that important?
    - if too many missing users, noise is incorrect? unless key dealer fixes it
  - noise is bounded (O(1) aggregation error)
     - still high but better than re-distributing
  - can we make this even better for a small subset of functioning users?
     - are there any concerns about a small subset?
     - describe your approach, (aggregator asks key dealer for noise corresponding to subset of non-functioning users) and get feedback
     - compare naive approach of redistributing keys, currently published approach of key dealer adding noise or proposed approach of aggregator requesting noise

## Secure execution ##
- is keystone a combination of sanctum (software) and some other project in hardware (aegis/ascend)?
  - sanctum assumes trusted target software. so basically that the software running in the enclave is not malicious. Nick, do you have any questions around this?
- what does it take to start experimenting with keystone? what is the keystone v1 release date?
- is the workshop material online?
- what would you recommend? computation against encrypted data or secure execution? is there a link to the pros and cons?
- open source hardware - what is the state of tooling?
- how do we access keystone hardware? do we have to actually burn an FPGA?
- model is: decrypt, process, encrypt. how do get the secret key to decrypt?
  - store data in datastore encrypted with user's SK
  - user stores SK on unshared device (e.g. phone)
  - user and enclave negotiate symmetric key exchange right after enclave verification
  - user sends data to enclave encrypted with SK
- Challenge: only user has SK, data is inaccessible without device storing SK (phone) being online. Conceptually, the enclave is like a little bit of the phone that can run cloud code.
  - consistent with signal and WhatsApp. Is this what they do under the hood?
  - how will this work for processing, e.g. re-running the pipeline?
    - users will need to launch **their** pipeline in an enclave on every sync
  - how will this work for aggregate queries?
    - users may be offline when the query is received, or may not want to participate over data plans
      - One idea: the query need not be instantaneous. Instead, it can include a time limit. The aggregator can poll users for data as long as the query is active. This means that users who come online only intermittently can also participate. Queries may take a day to run, but that is still faster than recruiting users and conducting a study.
      - We can improve performance by allowing the queries to include the number of users to participate. In that case, the aggregator can return as soon as the number of users has been reached, instead of waiting for the query to expire. This may also have implications in terms of the noise calculations and the protocol, but it is consistent with the previous issue of having data for only k/n users for a particular query.
  - how will the user retrieve data to be decrypted? Note that all data is stored encrypted, so nobody other than the user can see anything about it. Now assume that the user is trying to retrive some subset of the data, potentially to run algorithms against it, or to return it for a query. The naive approach is for the user to retrieve all the data, decrypt it, load it into a timeseries, and query against it. That also seems pretty inefficient. Options we discussed were:
    - storing the query keys without encryption (e.g. store the timestamps unencrypted). But this will leak information because we only store data when the user is in motion, so knowing when we have data will leak information on when we took trips. Also, we expect that aggregate queries will typically be gated by not just time but also by geolocation, and if you expose both time and geolocation then what is left
    - storing the data in subsets indexed by time blocks (e.g. each month). This may also leak some information (e.g. on the amount of travel per month) but maybe that is OK. Doesn't have a solution for the geolocation though
    - we can also estimate the data and see how bad the naive solution will be. Maybe we start with the naive solution and build in a performance optimization later.

## Experiment to set up a skeleton of secure execution for aggregate queries ##
Outline of an experiment to set up a skeleton of secure execution for aggregate
queries. This will allow us to verify that all the assumptions needed for the
enclave based aggregation of queries before we delve deeper into the
implementation.

Terminology:
`Q`: query script
`e_a`: enclave running the aggregation script `s_a`
`e_u1 ... e_un`: enclaves running the user script

Setup:
- Create two possible aggregator programs `s_a_valid` and `s_a_invalid`. Both
  programs will expect to receive messages from user enclaves, and will return
  a count of the number of messages received to the querier.
- Change the scripts slightly (potentially through dummy strings) to ensure
  that their hashes are slightly different.
- Record the hashes `h_a_valid` and `h_a_invalid`
- Create user programs that will receive an (IP address, valid hash) pair. The
  program should contact the enclave at the specified IP address and check its
  hash. If the hash is valid, send it the enclave an encrypted message. If the hash is
  invalid, ignore.
- Create a query script that takes three inputs:
    - `n`: number of users
    - `aggregator`: aggregator program
    - `valid hash`
  The script should spin up `e_a` with the specified aggregator program, and
  spin up `n` `e_u` with the (only, hardcoded) user program. It should then
  send messages to all the `e_u` with the IP of the aggregator and the
  specified valid hash. In the real world, the querier is untrusted and will not
  submit the valid hash, but let's do it this way for now to make it easy to
  test.
- Note that none of these programs have to be in python. They can be in C++ for
  now and we can figure out the integration with python once we know how to run
  python in an enclave

Tests:
- Run the query script with `s_a_valid` and `h_a_valid`. Expect the aggregator to return  `n`
- Run the query script with `s_a_invalid` and `h_a_valid`. Expect the aggregator to return  0
