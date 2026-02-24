# #508 - Limit the number of digits the real number policies can process. [Open]

> Username: djowel  
> Created at: 2019-05-14 23:38:01 UTC  
> Updated at: 2025-05-10 01:44:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/508  

See: https://github.com/boostorg/spirit/pull/282  
  
A better way to address this is to not allow such big digits in the default real number policies for primitive floating point numbers. The current code (real policies) limits the number of digits to traits::max_digits10<T>::value. The new ignore_excess_digits policy should also have a limit as well.

---

## Comment 1

> Username: saki7  
> Created at: 2025-05-10 01:43:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/508#issuecomment-2868210978  

I'm not sure if this applies to our current codebase.
