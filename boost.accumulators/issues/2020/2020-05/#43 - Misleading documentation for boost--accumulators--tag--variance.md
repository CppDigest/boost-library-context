# #43 - Misleading documentation for boost::accumulators::tag::variance [Open]

> Username: EricBackus  
> Created at: 2020-05-11 03:26:53 UTC  
> Updated at: 2020-05-11 03:26:53 UTC  
> Url: https://github.com/boostorg/accumulators/issues/43  

The first half of the documentation for variance is correct - it shows the formulas used for variance and mean. This formula is correct, and it is what is implemented in the boost code. (It implements a variant of the Welford method of computing variance.)  
  
But the second half of the documentation is, at best, misleading. The second half starts with "A simplification can be obtained by the approximate recursion...", then gives some formulas, and ends with "However, for small _n_ the difference can be non-negligible." This is misleading because this "simplification" is not, in fact, used by the boost code. And that's a good thing, because this "simplification" would produce incorrect results, and would not actually be any simpler. As far as I can tell, the second half of the documentation should be eliminated entirely.  
  
I note that the current situation leads some people to incorrectly believe that it is sometimes preferable to use boost::accumulators::tag::lazy_variance. For example, at [link to a stackoverflow question](https://stackoverflow.com/questions/7616511/calculate-mean-and-standard-deviation-from-a-vector-of-samples-in-c-using-boos), one person claims:  
  
> Note, that tag::variance calculates variance by an approximate formula. tag::variance(lazy) calculates by an exact formula, specifically: second moment - squared mean which will produce incorrect result if variance is very small because of rounding errors. It can actually produce negative variance. – panda-34 Dec 7 '15 at 13:36  
  
This person incorrectly thinks that tag::variance is only approximate.  
  
As far as I can tell, the _only_ reason to use tag::lazy_variance is if speed is your only consideration, since tag::lazy_variance is roughly twice as fast as tag::variance. But both are quite fast, so this is unlikely to be a consideration for most users.
