# #33 - [Foldable] Allow specifying the data type of the summed/multiplied objects in sum/product. [Closed]

> Username: ldionne  
> Created at: 2015-03-30 14:29:50 UTC  
> Updated at: 2015-04-02 01:34:57 UTC  
> Closed at: 2015-04-02 01:34:57 UTC  
> Url: https://github.com/boostorg/hana/issues/33  

Right now, we can't sum a tuple of an arbitrary Monoid because `zero<IntegralConstant<int>>` is used. It should be possible to specify the Monoid to use. One possibility is to add `sum.of<T>`, and to have `sum` be equivalent to `sum.of<IntegralConstant<int>>` by default.
