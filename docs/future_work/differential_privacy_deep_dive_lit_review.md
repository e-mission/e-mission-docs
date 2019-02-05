### Practial application ###

From [_A Practical Application of Diﬀerential Privacy to Personalized Online Advertising_](https://eprint.iacr.org/2011/152.pdf?fbclid=IwAR2EC4Gz0rjVv_-XCsEAiFcmp4XN_7vwU9PyL1H3RlAdoBN0Pm-nQnSZBkM)

I found section 2.1 particularly useful. For the classical definition of differential privacy, the attacker can choose the values of all entries except the ith entry, and for the ith entry, provide _x_i_ and _x'\_i_ that are possible matches. And the result of the query should not allow the user to distinguish between _x_i_ and _x'\_i_. That is pretty strong!
Mapping this to our case, let's say that the query is "how many people are in this building between 9am and noon"? We know the count result 0/1 for everybody except our user u. And the result needs to not reveal whether u was there or not. So we do this by adding noise.
Since the values of the others are known, I guess the argument is that the noise doesn't depend on the size of the database? 

> noise is added irrespective of the count results, and depends only on the privacy parameter ε.

That feels intuitively wrong, but I guess it is a stronger result if it does not. Also, then what is the distinction between large and small queries?

> very small campaigns (i.e., campaigns with a very small expected number of impressions and clicks). 

Their experimental methodology might also be interesting for us to try and get some kind of concrete intuition around this. It looks like they basically computed the direct result of the query, and the result of the query with added noise, and computed how far off the noisy result was. Which makes sense.

### Correlation ###

Comparing [_Privacy-Preserving Aggregation of Time-Series Data_](https://ssltest.cs.umd.edu/~elaine/docs/ndss2011.pdf) and [_Differentially private aggregation of distributed time-series with transformation and encryption_](http://dl.acm.org/citation.cfm?id=1807247),

The Shi/Song paper does not seem to address the notion of correlation at all. Their only consideration is that they basically support summation queries. So if I understand correctly, your function could even be to extract the lat/lng at a particular time point, although note that summing that would not be very useful :)


The Rastogi/Nath paper does take correlation seriously. In fact, if I understand correctly, one of their primary contributions is to deal with correlation. But, and maybe this was surprising only to me, their focus is primarily on improving utility rather than privacy of correlated queries.
Couple of things that I inferred from reading more closely with a focus on correlation:
- the data for each user can be correlated, and that is fine since we are reducing it to a scalar value that is (assumed to be) independent of similar scalar values from other users
- however, because the data for each user is correlated, the query results across time are also correlated. So if you counted the number of people on a particular stretch of street at `t1`, `t2`... `tn`, they would largely be correlated. The paper has a similar example with weights, if you have a fixed population, the average weight, or the number of people over 200lbs will be largely correlated across weeks.
   - I argued earlier that this is a feature, not a bug. For a lot of timeseries-based applications, we **want** to find outliers - e.g. data that **should be** correlated but is not - e.g. a statistically significant weight loss in the population, or a significant increase in usage of the road.
   - This should be fine as long as each query is individually differentially private - so if I can't tell whether a user was on a road at time `t1` and I can't tell that they are on the road at time `t2`, then I can't really reconstruct their trajectory.
     - Similarly, if I can't tell whether or not a user U was in a building between 12 and 1pm either this week or next week, then I can't tell whether they were the ones who changed their pattern.
- so the naive approach to correlated results, and the one used in the Shi/Song paper is to basically treat the queries individually and add the standard DP noise to each of them
- I **think** what the Rastogi/Nath paper is arguing is that this results in adding too much noise if we perform a set of repeated queries

    > For instance, standard differentially private techniques [7] can result in a noise of Θ(n) to each query answer, where n is the number of queries to answer, making the query answers practically useless if a long sequence of queries is to be answered.
    
    > An aggregate query can be a snapshot query... A query can also be recurring...
    
- I think that the intuition behind this is that if the sensitivity of a single count query is 1, then the sensitivity of repeated count queries is `n`.

    > Consider a query Q counting the number of users whose weight in month 1 is greater than 200 lb. Then ∆(Q) is simply 1 as Q can differ by at most 1 on adding/removing a sin- gle user’s data. Now consider Q = Q1, . . . , Qn, where Qi counts users whose weight in month i is greater than 200 lb. Then ∆1(Q) is n (for the pair I,I′ which differ in a single user having weight >
n (for the same pair I,I′).

If the number of repeated queries is large compared to the sample size  then basically, the overall noise is so high that **repeated** query is useless, e.g.
- you have 100 users and would like to query weekly over two years
- you have 100,000 users but would like a second-by-second sampling of the count on a street

They fix this with their frequency domain trick, which leads to the conclusion:

> To the best of our knowledge, FPAk is the first differentially private technique (unlike [11, 20]) that of- fers practical utility for time-series data.

### Sampling ###
Murtagh et al. in [_Usable Differential Privacy: A Case Study with PSI∗_](https://arxiv.org/pdf/1809.04103.pdf) include a discussion on how sampling can ensure greater privacy without increasing the value of the privacy parameters:

> Suppose we draw a random sample of rows from a database, and publish statistics about the sampled data instead of the full dataset. Sampling increases the uncertainty of the adversary about a user being in the sample. As a result, less noise is needed in order to satisfy the same level of privacy protection for the sampled data.

The technical details can be seen in Lemma 1. Subsampling improves the privacy loss parameters by a factor of roughly `m/n` where `n` is the sample size and `m` is the total amount of data that satisfies our query conditions. The paper also explains how subsampling would meet this condition: 

> As long as this subsample is truly random and the choice of the people in the subsample remain secret, the privacy amplification from secrecy of the sample comes for free. Because of the privacy/accuracy trade-off, these savings can actually be viewed as free boosts in accuracy while maintaining the same level of privacy.

This seems like we can take random samples of individuals that satisfy a certain query condition and keep the people and data in the subsample secret, then we can use a lower bound of overall noise.

### Privacy Parameters Assignment ###
In [_Usable Differential Privacy: A Case Study with PSI∗_](https://arxiv.org/pdf/1809.04103.pdf), users have the responsibility to assign their own privacy parameters when submitting their data. Users are informed how to set such parameters as PSI provides an introduction to differential privacy and recommendations for parameters in accessible but accurate documentation. 

1. Public information: It is not necessary to use differential privacy for public information. 
2. Information the disclosure of which would not cause material harm, but which the University has chosen to keep confidential: (`epsilon = 1`, `delta = 10^-5` ) 
3. Information that could cause risk of material harm to individuals or the University if disclosed: (`epsilon = 0.25`, `delta = 10^−6` ) 
4. Information that would likely cause serious harm to individuals or the University if disclosed: (`epsilon = 0.05`, `delta = 10^−7` ) 
5. Information that would cause severe harm to individuals or the University if disclosed: It is not recommended that the PSI tool be used with such severely sensitive data.

It is still not clear how to modify these privacy parameters if users choose to add or remove data. And it is unclear from this context how these general guidelines would change if we segmented our privacy budgets into months and location areas.

### Questions ###

- So even the classical technique doesn't appear to be a problem if the number of queries <<< number of users. Is this right?
   - (JS Response): I do think the classical solution (assuming we have `k` correlated queries, ensure privacy by multiplying the noise of each of the correlated queries by `k`) would work.
      - In a simple counting query example with 1,000,000 users and 12 correlated queries (global sensitivity is 1), the standard deviation  is around 200 which is a small percentage of the overall number of users.
      - But we should determine the threshold (in terms of deviation) for which we can use the classical technique as this is more efficient.
 
- And if it does, we can use their frequency domain trick, or maybe there is a non-patented version in the literature? Is this right?
   - (JS Response): Yes as long as we know the query workload (i.e. the number of correlated queries). I don't think this is practical as analysts will not typically know the number of queries they need ahead of time. Thus, it would be important to look more into methods that are adaptive to one at a time queries such as the Private Multiplicative Weights Mechanism.

- We have been worried for a while about the prospect of an attacker averaging out values from correlated queries to remove the noise. Why doesn't that happen in with the classical technique?
   - I guess this where the privacy budget comes in. So in that case, why would the user establish the privacy budget? If we know how much noise is added in each query, can't we determine the number of queries that are permitted before the noise can be averaged out? 
   - there is an intutive example with little math on page 12 of Lindell/Ormi, but I think that I need to digest that further. If anybody else got it, I'd love to get a tutorial...
   - the FPAk paper explicitly handles correlation between adjacent points; does it also handle correlation between points that are not adjacent (e.g. every wed morning at 10am, I arrive at work)


