# #578 - Copy 2 vectors into a single pair vector [Closed]

> Username: shehzan10  
> Created at: 2016-04-05 15:48:13 UTC  
> Updated at: 2016-04-27 02:45:23 UTC  
> Closed at: 2016-04-27 02:45:20 UTC  
> Url: https://github.com/boostorg/compute/issues/578  

I have 2 vectors of equal size. Say A and B.  
I want to create an vector of a `struct { type a; type b}` and copy the contents of A and B into this vector.  
I tried using transform, but couldn't figure it out.  
  
Here's what the C++11 code would look like (note: using std::pair instead of struct)  
  
```  
std::vector<std::pair<T, T> > X(N);  
std::transform(A.begin(), A.end(),  
                       B.begin(),  
                       std::back_inserter(X),  
                       [](T a, T b) { return std::make_pair(a, b); }  
                      );  
```  
  
How can I use the `back_inserter` and lamba in boost compute.  
Can I use `std::pair` as a type for `compute::vector`?  
  
Thanks!

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-04-05 16:10:51 UTC  
> Url: https://github.com/boostorg/compute/issues/578#issuecomment-205875779  

Check out  
https://github.com/boostorg/compute/blob/master/test/test_zip_iterator.cpp  
test case called "copy".  
5 kwi 2016 17:48 "Shehzan Mohammed" notifications@github.com napisał(a):  
  
> I have 2 vectors of equal size. Say A and B.  
> I want to create an vector of a struct { type a; type b} and copy the  
> contents of A and B into this vector.  
> I tried using transform, but couldn't figure it out.  
>   
> Here's what the C++11 code would look like (note: using std::pair instead  
> of struct)  
>   
> std::vector<std::pair<T, T> > X(N);  
> std::transform(A.begin(), A.end(),  
>                        B.begin(),  
>                        std::back_inserter(X),  
>                        [](T a, T b) { return std::make_pair(a, b); }  
>                       );  
>   
> How can I use the back_inserter and lamba in boost compute.  
> Can I use std::pair as a type for compute::vector?  
>   
> Thanks!  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/compute/issues/578

---

## Comment 2

> Username: shehzan10  
> Created at: 2016-04-05 17:52:34 UTC  
> Url: https://github.com/boostorg/compute/issues/578#issuecomment-205918351  

Thanks for that.  
Is it possible to use std::pair/tuple instead of `boost::tuple`?

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-04-05 18:06:59 UTC  
> Url: https://github.com/boostorg/compute/issues/578#issuecomment-205923732  

I'm not 100% sure, but I think it's possible to use `std::pair`/`std::tuple` and `std::make_pair`/`std::make_tuple`. You can check [test_pair.cpp](https://github.com/boostorg/compute/blob/38ba1c54479869c6ad6d2ea1f828e6d113bc5ab9/test/test_pair.cpp).

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-04-26 18:32:03 UTC  
> Url: https://github.com/boostorg/compute/issues/578#issuecomment-214841702  

@shehzan10 Did it work for you?

---

## Comment 5

> Username: shehzan10  
> Created at: 2016-04-26 18:37:31 UTC  
> Url: https://github.com/boostorg/compute/issues/578#issuecomment-214843324  

Yeah it did work with std::pair. But since the pair was being used in sort_by_key, we ran into other issues and dropped the pair and used a workaround.  
This issue can be closed.
