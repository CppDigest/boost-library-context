# #15 - from_just() returns a wrong value [Closed]

> Username: Iced-Sun  
> Created at: 2014-11-21 03:14:35 UTC  
> Updated at: 2014-11-24 14:53:08 UTC  
> Closed at: 2014-11-23 16:04:04 UTC  
> Url: https://github.com/boostorg/hana/issues/15  

Here's the snippet:  
  
``` c++  
auto a = maybe('x', compose(id, always), just(18))();  
std::cout << a << std::endl;   // 18  
  
auto b = maybe('x', compose(id, always), just(19));  
std::cout << b() << std::endl; // 19  
  
auto c = from_just(just(1)); // any integer other than 1 produces the same wrong result  
std::cout << c << std::endl;  // 4098!!!  
```  
  
And if the implementation of from_just in fwd/maybe.hpp is changed to:  
  
``` c++  
template <typename M>  
constexpr decltype(auto) operator()(M&& m) const {  
       return maybe(  
             error{}, compose(id, always), detail::std::forward<M>(m)  
       );  
}  
```  
  
then the result from_just(just(1))() is correct.  
  
hana, clang and llvm are all compliled from the the latest trunk.

---

## Comment 1

> Username: ldionne  
> Created at: 2014-11-21 20:24:09 UTC  
> Url: https://github.com/boostorg/hana/issues/15#issuecomment-64032270  

Thanks for the report. I think we're returning a dangling reference from `always`; I'll confirm this, add a test case and fix the issue when I get the time later today.

---

## Comment 2

> Username: pfultz2  
> Created at: 2014-11-22 01:18:20 UTC  
> Url: https://github.com/boostorg/hana/issues/15#issuecomment-64062265  

I found that `always` should be by value by default, and then by reference when it is explicitly requested by the user. That is the way it is at least implemented in the Fit library [here](http://pfultz2.github.io/Fit/doc/html/always/). I am not sure if that helps in your case or not.

---

## Comment 3

> Username: ldionne  
> Created at: 2014-11-22 21:23:45 UTC  
> Url: https://github.com/boostorg/hana/issues/15#issuecomment-64096019  

Thanks for the comment. Locally, I have a fix for this issue that does not require `always` to be by value all the time, but I'm interested to know the rationale for `always` being by value in Fit. Did you find use cases in which returning a reference is either unintuitive or downright wrong?

---

## Comment 4

> Username: pfultz2  
> Created at: 2014-11-24 05:55:44 UTC  
> Url: https://github.com/boostorg/hana/issues/15#issuecomment-64154562  

> Locally, I have a fix for this issue that does not require always to be by value all the time, but I'm interested to know the rationale for always being by value in Fit. Did you find use cases in which returning a reference is either unintuitive or downright wrong?  
  
Because it leads to bugs. Even though `always` captures an lvalue it doesn't mean that the function will be called in the same scope where the lvalue was captured. So its possible it could be referencing an out of scope variable:  
  
``` cpp  
auto always_squared(int n)  
{  
    int squared = n*n;  
    return always(squared);  
}  
  
auto f = always_squared(2);  
auto i = f(); // i is some junk value  
```  
  
So just as lambdas allow you to specify how you want to capture to avoid these mistakes, the `always` function does something similar. It captures by value by default since it is the "safer" options and then you can capture by reference in the cases you know the reference won't be used out of scope.   
  
Also the [`partial`](http://pfultz2.github.io/Fit/doc/html/partial/) adaptor does something similiar as well, since it essentially captures values to be called at a later time. And this also matches to how `std::bind` captures as well.

---

## Comment 5

> Username: ldionne  
> Created at: 2014-11-24 14:53:08 UTC  
> Url: https://github.com/boostorg/hana/issues/15#issuecomment-64204143  

Oh, I understand your comment now. The functional adaptors like `always` all capture by value in Hana too. I thought you were referring to the return type when calling `always(x)()`, which is an lvalue if `always(x)` is an lvalue, and an rvalue otherwise.
