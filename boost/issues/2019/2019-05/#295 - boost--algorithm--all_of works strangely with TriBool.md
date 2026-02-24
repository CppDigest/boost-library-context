# #295 - boost::algorithm::all_of works strangely with TriBool [Closed]

> Username: KordianD  
> Created at: 2019-05-27 16:12:27 UTC  
> Updated at: 2019-05-27 17:30:42 UTC  
> Closed at: 2019-05-27 17:30:42 UTC  
> Url: https://github.com/boostorg/boost/issues/295  

Recently I was  confused about boost::algorithm::all_of with TriBool.  
  
Minimum working example:  
  
```  
#include <iostream>  
#include "boost/logic/tribool.hpp"  
#include <vector>  
#include <boost/algorithm/cxx11/all_of.hpp>  
  
boost::logic::tribool isOdd (int) { return boost::logic::indeterminate; }  
  
int main()  
{  
   std::vector<int> test;  
   test.push_back(5);  
      
   if (boost::algorithm::all_of(test, isOdd)){  
       std::cout << "Is True";     
    }  
}  
```  
  
This example uses `TriBool`.  
  
Suppose that we have 2 elements in a vector.  
For one element the result is `boost::logic::indeterminate` and for the second one is `true`  
  
I can be wrong but for me `boost::algorithm::all_of` should evaluate to `false`.  
I assume that because when you use `boost::logic::indeterminate` in `if` statement it is evaluated to `false`.  
```  
if (some_value_with_indeterminate_state)  
{  
   std::cout << "Never executed";  
}  
```  
  
This is cause by:  
  
```  
template<typename InputIterator, typename Predicate>   
bool all_of ( InputIterator first, InputIterator last, Predicate p )  
{  
    for ( ; first != last; ++first )  
        if ( !p(*first))   
            return false;  
    return true;   
}   
```  
  
Suppose that in my case `p(*first)` is `boost::logic::indeterminate`  
  
`if ( !p(*first)) `  -->  `! indeterminate `  ---> negation of indeterminate gives indeterminate  
So finally, we get `if (indeterminate)` which is false --> after whole loop we get `true`

---

## Comment 1

> Username: mclow  
> Created at: 2019-05-27 16:34:55 UTC  
> Url: https://github.com/boostorg/boost/issues/295#issuecomment-496262166  

Consider the following code:  
```  
   if ( isOdd(5)) std::cout << "is odd!\n";  
   if (!isOdd(5)) std::cout << "is not odd!\n";  
```  
  
If you put that into your test program, you'll find that neither one of them is printed.  
That matches the behavior of `all_of` as well.

---

## Comment 2

> Username: mclow  
> Created at: 2019-05-27 16:37:34 UTC  
> Url: https://github.com/boostorg/boost/issues/295#issuecomment-496262677  

`all_of` applies the predicate to every element in the sequence, and if any of them fail (i.e, the predicate returns something that is "not true"), the it returns `false`.  
Otherwise, it returns `true`.  
That's what is happening here.

---

## Comment 3

> Username: KordianD  
> Created at: 2019-05-27 17:08:47 UTC  
> Updated at: 2019-05-27 17:10:33 UTC  
> Url: https://github.com/boostorg/boost/issues/295#issuecomment-496268171  

"(i.e, the predicate returns something **that is "not true"**), the it returns **false."**  
  
The predicate returns boost::logic::indeterminate which **is not true**, then it returns **true**  
  
If this behavior is okay, I am also okay with that but I found it in some sense non-intuitive and waste time debugging.

---

## Comment 4

> Username: mclow  
> Created at: 2019-05-27 17:20:11 UTC  
> Url: https://github.com/boostorg/boost/issues/295#issuecomment-496270031  

Then don't return TriBool from your predicate. `all_of` expects boolean results.
