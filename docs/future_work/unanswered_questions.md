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
