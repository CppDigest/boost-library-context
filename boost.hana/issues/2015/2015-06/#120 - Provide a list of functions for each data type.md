# #120 - Provide a list of functions for each data type [Closed]

> Username: ldionne  
> Created at: 2015-06-15 18:53:58 UTC  
> Updated at: 2015-08-25 16:11:27 UTC  
> Closed at: 2015-08-25 16:11:27 UTC  
> Url: https://github.com/boostorg/hana/issues/120  

Each data type should be able to provide a list of the methods that can be used with it.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-08-25 16:11:20 UTC  
> Url: https://github.com/boostorg/hana/issues/120#issuecomment-134654785  

Instead, I think it is better to provide a list of the concepts it models, as is done currently. Then, one can lookup one algorithm and check if it can be called on that data type by checking the constraints. Enumerating all the algorithms supported by a data type requires a huge duplication of information and I don't think that will help much for navigating the documentation.
