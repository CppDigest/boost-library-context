# #43 - Support creating function<> objects from lambda expressions [Closed]

> Username: kylelutz  
> Created at: 2014-01-07 03:33:04 UTC  
> Updated at: 2014-05-24 20:43:48 UTC  
> Closed at: 2014-05-24 20:43:48 UTC  
> Url: https://github.com/boostorg/compute/issues/43  

Add support for creating `function<>` objects from lambda expressions.  
  
For example, this should be possible: `boost::compute::function<int(int)> add_two = _1 + 2;`

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-05-24 20:43:48 UTC  
> Url: https://github.com/boostorg/compute/issues/43#issuecomment-44099328  

Implemented in 52886775f8b9ac52c8a44928e60df4c13481c6fd.
