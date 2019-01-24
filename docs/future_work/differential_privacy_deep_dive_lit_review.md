From [_A Practical Application of Diﬀerential Privacy to Personalized Online Advertising_](https://eprint.iacr.org/2011/152.pdf?fbclid=IwAR2EC4Gz0rjVv_-XCsEAiFcmp4XN_7vwU9PyL1H3RlAdoBN0Pm-nQnSZBkM)

I found section 2.1 particularly useful. For the classical definition of differential privacy, the attacker can choose the values of all entries except the ith entry, and for the ith entry, provide _x_i_ and _x'\_i_ that are possible matches. And the result of the query should not allow the user to distinguish between _x_i_ and _x'\_i_. That is pretty strong!
Mapping this to our case, let's say that the query is "how many people are in this building between 9am and noon"? We know the count result 0/1 for everybody except our user u. And the result needs to not reveal whether u was there or not. So we do this by adding noise.
Since the values of the others are known, I guess the argument is that the noise doesn't depend on the size of the database? 

> noise is added irrespective of the count results, and depends only on the privacy parameter ε.

That feels intuitively wrong, but I guess it is a stronger result if it does not. Also, then what is the distinction between large and small queries?

> very small campaigns (i.e., campaigns with a very small expected number of impressions and clicks). 

Their experimental methodology might also be interesting for us to try and get some kind of concrete intuition around this. It looks like they basically computed the direct result of the query, and the result of the query with added noise, and computed how far off the noisy result was. Which makes sense.

My questions after reading that paper are:
1. 

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

So even the classical technique doesn't appear to be a problem if the number of queries <<< number of users. And if it does, we can use their frequency domain trick, or maybe there is a non-patented version in the literature?
