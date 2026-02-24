# #58 - operator() and container unpacking [Closed]

> Username: HDembinski  
> Created at: 2018-07-06 08:22:31 UTC  
> Updated at: 2018-07-22 21:14:43 UTC  
> Closed at: 2018-07-22 21:14:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/58  

What happens when a user has 1d histogram with an axis that accepts std::string and fills with vector<std::string>. We want to unpack the vector. When user fills just a std::string, we want to pass this to the relevant axis. From the PoV of the code, both are containers, so how do we determine when it should be unpacked?

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-07-22 21:14:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/58#issuecomment-406897217  

We cannot always unpack. Checking whether unpacking would result in a type compatible with the axis would require an expensive run-time check for the dynamic histogram. I think we must keep the 1D case special, which prevents writing truly dimension-agnostic code on top of histogram.  
  
If the need arises, a special always_unpack wrapper type could be created for the filling. This would allow one to write dimension-agnostic code.
