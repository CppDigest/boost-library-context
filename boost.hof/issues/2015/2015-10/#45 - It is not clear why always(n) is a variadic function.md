# #45 - It is not clear why always(n) is a variadic function [Closed]

> Username: viboes  
> Created at: 2015-10-04 01:58:23 UTC  
> Updated at: 2015-12-22 06:20:17 UTC  
> Closed at: 2015-12-22 06:20:17 UTC  
> Url: https://github.com/boostorg/hof/issues/45  

Some design rational would be welcome

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 05:00:04 UTC  
> Url: https://github.com/boostorg/hof/issues/45#issuecomment-145430541  

From the definition:  
  
> The always function returns a function object that will always return the value given to it, no matter what parameters are passed to the function object.  
  
As such, it couldn't be anything other than variadic function.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:39:15 UTC  
> Url: https://github.com/boostorg/hof/issues/45#issuecomment-145689333  

I understand that given the definition the function must be variadic ;-)  
What I don't understand is why the always function need any parameter.  
For me always should return a nullary function. I understand that a variadic function is more general  
  
An example showing the use on a variadic context would be welcome.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-05 23:08:31 UTC  
> Url: https://github.com/boostorg/hof/issues/45#issuecomment-145694214  

The `always` function swallows the parameters, and returns the value. This is useful in many contexts. An example from the standard library, say we want to count all elements in a range, then we could implement this using `std::count_if`:  
  
``` cpp  
template<class Iterator, class T>  
auto count(Iterator first, Iterator last)  
{  
    return std::count_if(first, last, always(true));  
}  
  
```  
  
If `always` was only nullary then this wouldn't be possible.

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-06 00:02:25 UTC  
> Url: https://github.com/boostorg/hof/issues/45#issuecomment-145701869  

Great, good example to add.  
  
Vicente
