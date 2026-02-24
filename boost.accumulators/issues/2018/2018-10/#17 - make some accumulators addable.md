# #17 - [feature] make some accumulators addable [Open]

> Username: HDembinski  
> Created at: 2018-10-12 13:43:43 UTC  
> Updated at: 2018-10-12 13:44:14 UTC  
> Url: https://github.com/boostorg/accumulators/issues/17  

Some accumulators are addable: count, sum, mean, variance.  
The proposal is to support operator+ for these and for accumulator_set if all stored accumulators are addable.
