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
      - We can improve performance by allowing the queries to include the number of users to include. In that case, the aggregator can return as soon as the number of users has been reached, instead of waiting for the query to expire. This may also have implications in terms of the noise calculations and the protocol, but it is consistent with the previous issue of having data for only k/n users for a particular query.
