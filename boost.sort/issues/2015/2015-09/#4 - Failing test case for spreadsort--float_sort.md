# #4 - Failing test case for spreadsort::float_sort [Closed]

> Username: Morwenn  
> Created at: 2015-09-18 19:50:28 UTC  
> Updated at: 2016-04-05 13:18:50 UTC  
> Closed at: 2015-09-20 10:37:28 UTC  
> Url: https://github.com/boostorg/sort/issues/4  

I was doing benchmarks for one of my libraries which happens to provide a wrapper for `spreadsort`. The benchmarks are always followed by a call to `std::is_sorted` so that they somehow double as unit tests. During one of the benchmarks, the assertion fired for `spreadsort::float_sort`. Here is a simple test case to reproduce the error:  
  
```  
#include <algorithm>  
#include <cassert>  
#include <cstddef>  
#include <vector>  
#include <boost/sort/sort.hpp>  
  
int main()  
{  
    static constexpr std::size_t size = 1000000;  
  
    std::vector<double> vec;  
    for (int i = 0; i < size; ++i) vec.push_back(i);  
    for (int i = 0; i < size; i += 2) vec[i] *= -1;  
  
    boost::sort::spreadsort::float_sort(vec.begin(), vec.end());  
    assert(std::is_sorted(vec.begin(), vec.end())); // fires  
}  
```  
  
I got the error with the latest g++ and clang++, both under Windows and Linux. The error appears with both `float` and `double`. There is no error when `long double` is used, but I guess it's because `spreadsort` falls back to `std::sort` to sort collections of `long double` elements.

---

## Comment 1

> Username: spreadsort  
> Created at: 2015-09-19 10:45:18 UTC  
> Url: https://github.com/boostorg/sort/issues/4#issuecomment-141644036  

Good find Morwenn.  I've found the bug with some negative floats that  
random number testing didn't catch, and am working on a fix.  
  
On Fri, Sep 18, 2015 at 3:50 PM Morwenn notifications@github.com wrote:  
  
> I was doing benchmarks for one of my libraries which happens to provide a  
> wrapper for spreadsort. The benchmarks are always followed by a call to  
> std::is_sorted so that they somehow double as unit tests. During one of  
> the benchmarks, the assertion fired for spreadsort::float_sort. Here is a  
> simple test case to reproduce the error:  
>   
> #include <algorithm>  
> #include <cassert>  
> #include <cstddef>  
> #include <vector>  
> #include <boost/sort/sort.hpp>  
>   
> int main()  
> {  
>     static constexpr std::size_t size = 1000000;  
>   
> ```  
> std::vector<double> vec;  
> for (int i = 0; i < size; ++i) vec.push_back(i);  
> for (int i = 0; i < size; i += 2) vec[i] *= -1;  
>   
> boost::sort::spreadsort::float_sort(vec.begin(), vec.end());  
> assert(std::is_sorted(vec.begin(), vec.end())); // fires  
> ```  
>   
> }  
>   
> I got the error with the latest g++ and clang++, both under Windows and  
> Linux.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/sort/issues/4.

---

## Comment 2

> Username: spreadsort  
> Created at: 2015-09-20 01:53:08 UTC  
> Url: https://github.com/boostorg/sort/issues/4#issuecomment-141724789  

Ok, the bug was that negative floats in reverse order were causing my test for data already in sorted order to give a false positive, and the algorithm exited out.  I created tests first (for both positives and negatives in sorted and reverse-sorted order, along with your test), and verified that they failed.  This is because negatives sort in reverse order from their integer representation.  I fixed it by using float comparison to check the floats for already-sorted order, and verified the fix.  It's now in develop.  I'll roll it into master once I've investigated your other issue.  
  
It's understandable that this didn't trigger with random data, because the chances of getting a chunk of 2000 floats in exact reverse order with random inputs is extremely small.

---

## Comment 3

> Username: spreadsort  
> Created at: 2015-09-20 03:46:03 UTC  
> Url: https://github.com/boostorg/sort/issues/4#issuecomment-141742515  

I've committed both changes to master.  Please redownload and confirm that both issues you saw (this incorrect sorting, and wstring) are fixed, and I'll close the bugs.

---

## Comment 4

> Username: Morwenn  
> Created at: 2015-09-20 10:03:25 UTC  
> Url: https://github.com/boostorg/sort/issues/4#issuecomment-141769437  

I have incorporated your fixes and run my tests and benchmarks again for both `double` and `float`. Everything seems to work and to be consistent. Congratulations for finding the root of the problem and fixing it quickly with such a test case :)

---

## Comment 5

> Username: spreadsort  
> Created at: 2015-09-20 10:37:28 UTC  
> Url: https://github.com/boostorg/sort/issues/4#issuecomment-141774647  

Thanks again for finding it!  
I'd be interested to hear how your library uses spreadsort if you care to share.

---

## Comment 6

> Username: Morwenn  
> Created at: 2015-09-20 10:48:38 UTC  
> Updated at: 2016-04-05 13:18:50 UTC  
> Url: https://github.com/boostorg/sort/issues/4#issuecomment-141774971  

Well, it only wraps it to provide a `spread_sorter` to users. Honestly, the library does not solve any real problem, it's more of an "interface" experiment to see what can be done about sorting stuff (trying to encompass the concepts of comparison sorters, non-comparison sorters, iterator categories, stability...). I wanted to check whether its support for non-comparison sorters was good enough, and your spreadsort happened to be an excellent guinea pig :p  
  
Anyway, here is the library: https://github.com/Morwenn/cpp-sort

---

## Comment 7

> Username: spreadsort  
> Created at: 2015-09-21 01:53:19 UTC  
> Url: https://github.com/boostorg/sort/issues/4#issuecomment-141854884  

Ok, thanks
