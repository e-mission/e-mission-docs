## Privacy Improvements ##
Location data is very privacy sensitive
(http://www.nature.com/doifinder/10.1038/srep01376). This is particularly true
for a location _timeline_, since anonymization techniques fail if there are
repeated patterns in the data. For example, identifying a users' most common
location at night and during the day can help us identify their home and work
locations.

But these repeated patterns are also very useful. At the personal level, we
want to give people recommendations for their most common trips, or use their
patterns to infer the characteristics of their travel. At the structural
level, we want to aggregate the data from citizens to see repeated problems
that can be targeted for fixing.

The primary difference between the intrusive and useful privacy-sensitive analyses
is control and ownership.

Users should own their data, the analysis that runs on it, and the way
in which the results are used. While users do currently get to control what raw
data is collected from their smartphones, once the data has been collected,
they lose control over it, and it can be re-shared in ways that they do not
expect. This argues for a more mediated sharing experience at a higher level in
the data stack.

Note that this high level overview recurs in many scenarios, and there is ongoing work,
including in the RISE lab, around addressing some of these issues. We may be able to use
some of these in our own work. For example, check out 
[this talk from Dawn Song](https://keystone-enclave.org/files/dawn-nsf-2018-v5.pdf).

This level of control involves integrating two existing research areas.

### Computation on encrypted data/secure execution ###
Conceptually, for users to own their own data, they need to run a server that
they maintain and that collects their data. They can then choose to install
the analysis scripts that they want on the server, and see the results
themselves.

Since few users run their own servers, this will typically involve storing
encrypted data on shared infrastructure. But then how can users run algorithms
against this encrypted data?

- One option is to run algorithms directly against encrypted data. There has
  been prior work on [searching encrypted text directly](https://people.eecs.berkeley.edu/~raluca/mylar.pdf) and decrypting it in the browser to display the text. However, for maximum flexibility and performance, we typically want to run analysis on servers instead of user phones, and to have access to the results to participate in aggregate queries. There has been more recent work in this area from the RISE lab, some potentially unpublished, which we should explore.
    - [Opaque: An Oblivious and Encrypted Distributed Analytics Platform](https://people.eecs.berkeley.edu/~wzheng/opaque.pdf)
    - [Machine Learning Classification Over Encrypted Data](https://eprint.iacr.org/2014/331)

- A second option is to use stored keys to decrypt the data before processing,
  and run the processing in a secure execution environment (e.g. SGX).
  Unfortunately, in order to decrypt the data, we need to store private keys, and
  have them accessible on the server for decryption. But then if the private
  keystore manager is compromised, then the attacker has access to your private
  key and all your data.  Do web of trust/delegated authorization schemes (such as WAVE)[https://docs.google.com/document/d/1MGoL5wgVPyIdDJnEwA3We9USi3ZDKNy2pou_sppx6w8/edit?usp=sharing]
solve this problem? And once the data is decrypted, root on the server where it
is running will have access to it, which is why it needs to run in a hardware
enclave(https://keystone-enclave.org/). Does it make sense to run full algorithms in a hardware enclave? It looks like it is currently used primarily for contract checking. Will the overhead be too high?
    - https://keystone-enclave.org/
    - https://en.wikipedia.org/wiki/Trusted_execution_environment

### Privacy-preserving aggregation ###
For structural analysis, we need to see results across a wide range of users.
Conceptually, users can also choose to participate in aggregate queries for
results, with controls for which results to share and how much aggregation they
want to participate in.

Related work:
- [OpenPDS: Protecting the Privacy of Metadata through SafeAnswers](https://doi.org/10.1371/journal.pone.0098790)
- [PDVLoc: A Personal Data Vault for Controlled Location Data Sharing](https://doi.org/10.1145/2523820)

This kind of aggregate query is typically handled using differential privacy.
However, differential privacy is challenging for timeseries data since it has
so much structure. However, there has been prior work on differential privacy
for certain kinds of aggregate queries against timeseries data.

Related work:
- [Privacy-Preserving Aggregation of Time-Series Data](https://amplab.cs.berkeley.edu/publication/privacy-preserving-aggregation-of-time-series-data/)
- [Differentially private aggregation of distributed time-series with transformation and encryption](http://dl.acm.org/citation.cfm?id=1807247)
