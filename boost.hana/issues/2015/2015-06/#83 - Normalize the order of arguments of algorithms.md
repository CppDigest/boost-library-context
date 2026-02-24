# #83 - Normalize the order of arguments of algorithms [Closed]

> Username: ldionne  
> Created at: 2015-06-05 00:45:37 UTC  
> Updated at: 2015-06-05 16:29:11 UTC  
> Closed at: 2015-06-05 16:29:11 UTC  
> Url: https://github.com/boostorg/hana/issues/83  

We don't want `at(n, xs)`, `drop(n, xs)`, but `slice(xs, from, to)`. The sequence should always come first, except in rare cases. Algorithms that must be normalized are:  
- [x] `at(n, xs)`  
- [x] `drop(n, xs)`, `drop.at_most(n, xs)` and `drop.exactly(n, xs)`  
- [x] `take(n, xs)`, `take.at_most(n, xs)` and `take.exactly(n, xs)`  
- [x] `remove_at(n, xs)`   
- [x] `suffix(z, xs)`  
- [x] `cycle(n, xs)`  
- [x] `repeat(n, x)`
