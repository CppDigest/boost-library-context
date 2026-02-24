# #85 - Weird behavior of integer_sort with a key array [Closed]

> Username: prclibo  
> Created at: 2024-10-20 01:44:33 UTC  
> Updated at: 2024-10-26 09:45:50 UTC  
> Closed at: 2024-10-20 11:23:55 UTC  
> Url: https://github.com/boostorg/sort/issues/85  

Hi, I would like to argsort an array of indices based on their keys. I used the following code with a lambda to query keys, but the indices are not sorted by keys, which I think is weird. Please any suggestion on this:)  
  
```  
#include <boost/sort/spreadsort/integer_sort.hpp>  
#include <vector>  
#include <iostream>  
  
int main() {  
    std::vector<int> keys = {4, 3, 5, 1};  
    std::vector<int> pt_idxs = {0, 1, 2, 3};  
    boost::sort::spreadsort::integer_sort(pt_idxs.begin(), pt_idxs.end(),  
        [&keys](int i, unsigned o) { return keys[i] >> o; });  
  
    for (const auto& idx : pt_idxs) { std::cout << idx << " "; }  
    std::cout << std::endl;  
    std::cout << "------------" << std::endl;  
  
    pt_idxs = {2, 1, 0, 3};  
    boost::sort::spreadsort::integer_sort(pt_idxs.begin(), pt_idxs.end(),  
        [&keys](int i, unsigned o) { return keys[i] >> o; });  
  
    for (const auto& idx : pt_idxs) { std::cout << idx << " "; }  
    std::cout << std::endl;  
  
    return 0;  
}  
```  
  
Output:  
```  
0 1 2 3  
------------  
0 1 2 3  
```

---

## Comment 1

> Username: spreadsort  
> Created at: 2024-10-20 02:31:13 UTC  
> Url: https://github.com/boostorg/sort/issues/85#issuecomment-2424433734  

You're only overriding the shift operator; you also need to override the  
comparison operator if you don't want default comparison.  The spreadsort  
library does hybrid sorting, so it uses radix based (shift) or comparison  
based depending on which has better worst case performance for the bucket  
it is considering.  In this case there are less than 1000 elements total in  
the input, so it jumps straight to comparison based sorting, and since you  
didn't override the comparison operator, it generates the results you're  
seeing using the default comparison operator.  
  
I think you should use the function with 4 parameters:  
https://live.boost.org/doc/libs/1_69_0/libs/sort/doc/html/boost/sort/spreadsort/integer_sort_idp51986944.html  
  
On Sat, Oct 19, 2024, 9:44 PM Bo Li ***@***.***> wrote:  
  
> Hi, I would like to argsort an array of indices based on their keys. I  
> used the following code with a lambda to query keys, but the indices are  
> not sorted by keys, which I think is weird. Please any suggestion on this:)  
>  
> #include <boost/sort/spreadsort/integer_sort.hpp>  
> #include <vector>  
> #include <iostream>  
>  
> int main() {  
>     std::vector<int> keys = {4, 3, 5, 1};  
>     std::vector<int> pt_idxs = {0, 1, 2, 3};  
>     boost::sort::spreadsort::integer_sort(pt_idxs.begin(), pt_idxs.end(),  
>         [&keys](int i, unsigned o) { return keys[i] >> o; });  
>  
>     for (const auto& idx : pt_idxs) { std::cout << idx << " "; }  
>     std::cout << std::endl;  
>     std::cout << "------------" << std::endl;  
>  
>     pt_idxs = {2, 1, 0, 3};  
>     boost::sort::spreadsort::integer_sort(pt_idxs.begin(), pt_idxs.end(),  
>         [&keys](int i, unsigned o) { return keys[i] >> o; });  
>  
>     for (const auto& idx : pt_idxs) { std::cout << idx << " "; }  
>     std::cout << std::endl;  
>  
>     return 0;  
> }  
>  
> Output:  
>  
> 0 1 2 3  
> ------------  
> 0 1 2 3  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/85>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABG2HXBN5YTQA4OGUFKUEKDZ4MDJLAVCNFSM6AAAAABQIANONSVHI2DSMVQWIX3LMV43ASLTON2WKOZSGU4TSOBSGE3TEOI>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 2

> Username: spreadsort  
> Created at: 2024-10-20 11:23:55 UTC  
> Url: https://github.com/boostorg/sort/issues/85#issuecomment-2424862838  

Incorrect usage.

---

## Comment 3

> Username: prclibo  
> Created at: 2024-10-26 08:45:14 UTC  
> Url: https://github.com/boostorg/sort/issues/85#issuecomment-2439435420  

> You're only overriding the shift operator; you also need to override the comparison operator if you don't want default comparison. The spreadsort library does hybrid sorting, so it uses radix based (shift) or comparison based depending on which has better worst case performance for the bucket it is considering. In this case there are less than 1000 elements total in the input, so it jumps straight to comparison based sorting, and since you didn't override the comparison operator, it generates the results you're seeing using the default comparison operator. I think you should use the function with 4 parameters: https://live.boost.org/doc/libs/1_69_0/libs/sort/doc/html/boost/sort/spreadsort/integer_sort_idp51986944.html  
> […](#)  
> On Sat, Oct 19, 2024, 9:44 PM Bo Li ***@***.***> wrote: Hi, I would like to argsort an array of indices based on their keys. I used the following code with a lambda to query keys, but the indices are not sorted by keys, which I think is weird. Please any suggestion on this:) #include <boost/sort/spreadsort/integer_sort.hpp> #include <vector> #include <iostream> int main() { std::vector<int> keys = {4, 3, 5, 1}; std::vector<int> pt_idxs = {0, 1, 2, 3}; boost::sort::spreadsort::integer_sort(pt_idxs.begin(), pt_idxs.end(), [&keys](int i, unsigned o) { return keys[i] >> o; }); for (const auto& idx : pt_idxs) { std::cout << idx << " "; } std::cout << std::endl; std::cout << "------------" << std::endl; pt_idxs = {2, 1, 0, 3}; boost::sort::spreadsort::integer_sort(pt_idxs.begin(), pt_idxs.end(), [&keys](int i, unsigned o) { return keys[i] >> o; }); for (const auto& idx : pt_idxs) { std::cout << idx << " "; } std::cout << std::endl; return 0; } Output: 0 1 2 3 ------------ 0 1 2 3 — Reply to this email directly, view it on GitHub <[#85](https://github.com/boostorg/sort/issues/85)>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/ABG2HXBN5YTQA4OGUFKUEKDZ4MDJLAVCNFSM6AAAAABQIANONSVHI2DSMVQWIX3LMV43ASLTON2WKOZSGU4TSOBSGE3TEOI> . You are receiving this because you are subscribed to this thread.Message ID: ***@***.***>  
  
Thank you for the explanation! Now it works, though not significantly faster than std::sort on a 100k-array. But thank you for the help!:)

---

## Comment 4

> Username: spreadsort  
> Created at: 2024-10-26 09:45:49 UTC  
> Url: https://github.com/boostorg/sort/issues/85#issuecomment-2439452146  

I optimized spreadsort a while ago for the processors at the time.  You are  
welcome to adjust the constants to your specific sorting problem:  
https://github.com/boostorg/sort/blob/develop/include/boost/sort/spreadsort/detail/constants.hpp  
  
On processors with a fast and large L2 cache the optimal max_splits can be  
much larger (say 16).  Increasing int_log_min_split_count may also help if  
your index function is slow relative to comparison.  
  
  
On Sat, Oct 26, 2024, 4:45 AM Bo Li ***@***.***> wrote:  
  
> You're only overriding the shift operator; you also need to override the  
> comparison operator if you don't want default comparison. The spreadsort  
> library does hybrid sorting, so it uses radix based (shift) or comparison  
> based depending on which has better worst case performance for the bucket  
> it is considering. In this case there are less than 1000 elements total in  
> the input, so it jumps straight to comparison based sorting, and since you  
> didn't override the comparison operator, it generates the results you're  
> seeing using the default comparison operator. I think you should use the  
> function with 4 parameters:  
> https://live.boost.org/doc/libs/1_69_0/libs/sort/doc/html/boost/sort/spreadsort/integer_sort_idp51986944.html  
> … <#m_-5415155411875419621_>  
> On Sat, Oct 19, 2024, 9:44 PM Bo Li *@*.*> wrote: Hi, I would like to  
> argsort an array of indices based on their keys. I used the following code  
> with a lambda to query keys, but the indices are not sorted by keys, which  
> I think is weird. Please any suggestion on this:) #include  
> <boost/sort/spreadsort/integer_sort.hpp> #include #include int main() {  
> std::vector keys = {4, 3, 5, 1}; std::vector pt_idxs = {0, 1, 2, 3};  
> boost::sort::spreadsort::integer_sort(pt_idxs.begin(), pt_idxs.end(),  
> [&keys](int i, unsigned o) { return keys[i] >> o; }); for (const auto& idx  
> : pt_idxs) { std::cout << idx << " "; } std::cout << std::endl; std::cout  
> << "------------" << std::endl; pt_idxs = {2, 1, 0, 3};  
> boost::sort::spreadsort::integer_sort(pt_idxs.begin(), pt_idxs.end(),  
> [&keys](int i, unsigned o) { return keys[i] >> o; }); for (const auto& idx  
> : pt_idxs) { std::cout << idx << " "; } std::cout << std::endl; return 0; }  
> Output: 0 1 2 3 ------------ 0 1 2 3 — Reply to this email directly, view  
> it on GitHub <#85 <https://github.com/boostorg/sort/issues/85>>, or  
> unsubscribe  
> https://github.com/notifications/unsubscribe-auth/ABG2HXBN5YTQA4OGUFKUEKDZ4MDJLAVCNFSM6AAAAABQIANONSVHI2DSMVQWIX3LMV43ASLTON2WKOZSGU4TSOBSGE3TEOI  
> <https://github.com/notifications/unsubscribe-auth/ABG2HXBN5YTQA4OGUFKUEKDZ4MDJLAVCNFSM6AAAAABQIANONSVHI2DSMVQWIX3LMV43ASLTON2WKOZSGU4TSOBSGE3TEOI>  
> . You are receiving this because you are subscribed to this thread.Message  
> ID: @.*>  
>  
> Thank you for the explanation! Now it works, though not significantly  
> faster than std::sort on a 100k-array. But thank you for the help!:)  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/85#issuecomment-2439435420>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABG2HXBBM6WNC7NLAUCMFPTZ5NJDBAVCNFSM6AAAAABQIANONSVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDIMZZGQZTKNBSGA>  
> .  
> You are receiving this because you modified the open/close state.Message  
> ID: ***@***.***>  
>
