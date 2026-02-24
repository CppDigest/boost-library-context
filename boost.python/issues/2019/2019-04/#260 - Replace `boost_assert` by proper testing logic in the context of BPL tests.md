# #260 - Replace `boost_assert` by proper testing logic in the context of BPL tests. [Open]

> Username: stefanseefeld  
> Created at: 2019-04-01 00:39:29 UTC  
> Updated at: 2019-04-01 00:39:29 UTC  
> Url: https://github.com/boostorg/python/issues/260  

Boost.Python uses calls to `boost_assert` in its testing infrastructure. That macro suffers the same problems as the standard `assert` macro: it may evaluate to a no-op if `NDEBUG` is defined.  
  
We need to find an alternative (which doesn't have to be a macro at all !), and make the corresponding adjustments throughout the Boost.Python test base.
