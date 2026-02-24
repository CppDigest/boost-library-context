# #275 - Bug in sort_by_key [Closed]

> Username: shehzan10  
> Created at: 2014-10-08 16:31:48 UTC  
> Updated at: 2014-12-15 19:13:20 UTC  
> Closed at: 2014-12-15 19:13:20 UTC  
> Url: https://github.com/boostorg/compute/issues/275  

I have been using this library for a short while now. I was working primarily with sort_by_key and I found a bug.  
  
In general terms, here is an overview:  
I want a MxN matrix created as a 1D vector and sort each 0th-dimension (or row).  
  
I create a `compute::vector<int> vector(MxN)`.  
I create a secondary vector that stores indices, ie. values 0 to (M*N -1).  
  
```  
compute::vector<int> index(M*N);  
compute::copy(  
            compute::counting_iterator<int>(0),  
            compute::counting_iterator<int>(64),  
            ix.begin());  
```  
  
Ps. It would be great to have a "sequence" function similar to "fill" which does the above.  
  
Now, I want to sort each 0-th dimension independently  
  
```  
for(int i = 0; i < N; i++) {  
    compute::sort_by_key(vector.begin() + i * M, vector.begin() + (i + 1) * M,  
                                         index.begin() + i * M);  
}  
```  
  
This sorts only the values for i = 0. Any other values do not get sorted.  
This failure is not type specific, it fails for float, double etc as well.  
  
However, if I add `compute::less<int>()` to the function arguments, then all values get sorted.  
  
You can find my entire code and output here: https://gist.github.com/shehzan10/c76b39fa267da2e7715d  
It's a simple modification for the sort_vector.cpp example.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-10-08 18:31:41 UTC  
> Url: https://github.com/boostorg/compute/issues/275#issuecomment-58404622  

Interesting, I'll look into this more. Thanks for reporting it!  
  
By the way, the sequence function exists, but it's called [`iota()`](http://kylelutz.github.io/compute/boost/compute/iota.html) (named after the `iota()` algorithm in C++11).

---

## Comment 2

> Username: shehzan10  
> Created at: 2014-10-08 18:51:08 UTC  
> Url: https://github.com/boostorg/compute/issues/275#issuecomment-58407725  

Thanks for letting me know!

---

## Comment 3

> Username: shehzan10  
> Created at: 2014-12-15 19:13:20 UTC  
> Url: https://github.com/boostorg/compute/issues/275#issuecomment-67047759  

Looks like this was fixed at some point. Retesting the sample code provided gives the correct output.
