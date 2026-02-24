# #11 - Using counting_sort to improve integer_spread_sort [Closed]

> Username: Morwenn  
> Created at: 2016-05-19 20:00:39 UTC  
> Updated at: 2017-11-22 02:42:08 UTC  
> Closed at: 2017-11-22 02:42:08 UTC  
> Url: https://github.com/boostorg/sort/issues/11  

Hi, it's me again :)  
  
No bug or memory leak today, but I was benchmarking some sorting algorithms again and was surprised by how fast [a simple counting sort](http://stackoverflow.com/a/37105668/1364752). Here are the results of a benchmark testing several sorting algorithms against collections of one million of integer elements with several data distributions:  
  
![counting_sort](https://cloud.githubusercontent.com/assets/1164683/15407401/ee30da14-1e0a-11e6-927f-c550efc1925d.png)  
  
While not always the fastest, counting sort tends to be insanely fast and always beats spreadsort in the benchmarks. Of course, counting sort has its share of problems, but I believe that it can be used to improve the integer-only version of spreadsort that does not take any custom comparison or shift function. Basically, we could add the following implementation somewhere (this version uses C++11, but it can be done in C++03):  
  
```  
template<class ForwardIter, class T>  
void counting_sort(ForwardIter first, ForwardIter last, T min, T max) {  
  if (min == max) return;  
  
  using difference_type = typename std::iterator_traits<ForwardIterator>::difference_type;  
  std::vector<difference_type> counts(max - min + 1, 0);  
  
  for (auto it = first ; it != last ; ++it) {  
    ++counts[*it - min];  
  }  
  
  for (auto count: counts) {  
    first = std::fill_n(first, count, min++);  
  }  
}  
```  
  
Then we could modify the beginning of `spreadsort_rec` to use it as follows:  
  
```  
template <class RandomAccessIter, class Div_type, class Size_type>  
inline void  
spreadsort_rec(RandomAccessIter first, RandomAccessIter last,  
          std::vector<RandomAccessIter> &bin_cache, unsigned cache_offset  
          , size_t *bin_sizes)  
{  
  //This step is roughly 10% of runtime, but it helps avoid worst-case  
  //behavior and improve behavior with real data  
  //If you know the maximum and minimum ahead of time, you can pass those  
  //values in and skip this step for the first iteration  
  RandomAccessIter max, min;  
  if (is_sorted_or_find_extremes(first, last, max, min))  
    return;  
  
  if (*max - *min <= last - first) {  
    counting_sort(first, last, *min, *max);  
    return;  
  }  
  
  // Rest of the current implementation...  
}  
```  
  
Thanks to `is_sorted_or_find_extremes`, we already know the minimum and maximum values of the range `[first, last)`, so we don't have to compute them again in `counting_sort`. The check `*max - *min <= last - first` ensures that `counting_sort` will be called only if the `std::vector` it needs to allocate is no bigger than the original array, which means that spreadsort keeps a O(n) auxiliary memory guarantee (IIRC it is the current memory guarantee of spreadsort, right?). That check also ensures that the algorithm won't even run into the pathological worst cases of counting sort, so we only keep the good parts.  
  
In other words, we can significantly speed up some scenarios at the cost of a single condition that shouldn't even weight too much when it fails. If I find the time, I will try to compare a version of spreadsort with this trick against the current one and share the benchmark results here.

---

## Comment 1

> Username: spreadsort  
> Created at: 2016-05-20 10:54:27 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-220576352  

On Thu, May 19, 2016, 4:08 PM Morwenn notifications@github.com wrote:  
  
> Hi, it's me again :)  
>   
> No bug or memory leak today, but I was benchmarking some sorting  
> algorithms again and was surprised by how fast a simple counting sort  
> http://stackoverflow.com/a/37105668/1364752. Here are the results of a  
> benchmark testing several sorting algorithms against collections of one  
> million of integer elements with several data distributions:  
>   
> [image: counting_sort]  
> https://cloud.githubusercontent.com/assets/1164683/15407401/ee30da14-1e0a-11e6-927f-c550efc1925d.png  
>   
> While not always the fastest, counting sort tends to be insanely fast and  
> always beats spreadsort in the benchmarks. Of course, counting sort has its  
> share of problems, but I believe that it can be used to improve the  
> integer-only version of spreadsort that does not take any custom comparison  
> or shift function. Basically, we could add the following implementation  
> somewhere (this version uses C++11, but it can be done in C++03):  
>   
> Counting sort is O(k+N) where k is the range of possible key values.  
> Usually k >> N, making counting sort unusable.  What is your range of key  
> values?  How many unique key values are in the set?  
> Most sorts are pretty fast when the range is small.  
>   
> template<class ForwardIter, class T>  
> void counting_sort(ForwardIter first, ForwardIter last, T min, T max) {  
>   if (min == max) return;  
>   
>   using difference_type = typename std::iterator_traits<ForwardIterator>::difference_type;  
>   std::vector<difference_type> counts(max - min + 1, 0);  
>   
>   for (auto it = first ; it != last ; ++it) {  
>     ++counts[*it - min];  
>   }  
>   
>   for (auto count: counts) {  
>     first = std::fill_n(first, count, min++);  
>   }  
> }  
>   
> Then we could modify the beginning of spreadsort_rec to use it as follows:  
>   
> template <class RandomAccessIter, class Div_type, class Size_type>  
> inline void  
> spreadsort_rec(RandomAccessIter first, RandomAccessIter last,  
>           std::vector<RandomAccessIter> &bin_cache, unsigned cache_offset  
>           , size_t *bin_sizes)  
> {  
>   //This step is roughly 10% of runtime, but it helps avoid worst-case  
>   //behavior and improve behavior with real data  
>   //If you know the maximum and minimum ahead of time, you can pass those  
>   //values in and skip this step for the first iteration  
>   RandomAccessIter max, min;  
>   if (is_sorted_or_find_extremes(first, last, max, min))  
>     return;  
>   
>   if (*max - *min <= last - first) {  
>     counting_sort(first, last, *min, *max);  
>     return;  
>   }  
>   
>   // Rest of the current implementation...  
> }  
>   
> Thanks to is_sorted_or_find_extremes, we already know the minimum and  
> maximum values of the range [first, last), so we don't have to compute  
> them again in counting_sort. The check *max - *min <= last - first  
> ensures that counting_sort will be called only if the std::vector it  
> needs to allocate is no bigger than the original array, which means that  
> spreadsort keeps a O(n) auxiliary memory guarantee (IIRC it is the current  
> memory guarantee of spreadsort, right?).  
>   
> In other words, we can significantly speed up some scenarios at the cost  
> of a single condition that shouldn't even weight too much when it fails. If  
> I find the time, I will try to compare a version of spreadsort with this  
> trick against the current one and share the benchmark results here.  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/sort/issues/11

---

## Comment 2

> Username: Morwenn  
> Created at: 2016-05-20 11:49:10 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-220585612  

The check `if (*max - *min <= last - first)` ensures that counting sort is called only when k <= N.  
  
For example in the test « Shuffled » there are one million elements ranging from 0 to 1000000 while in the test « Shuffled (16 values) » there are one million elements ranging from 0 to 15. Basically, unless when there is an explicit « 16 values », every value is unique, even though the size of the range never exceeds the number of values (except maybe for « Alternating » where it might be k = 2N). Even in spreadsort's best cases, we need to have something akin to k > 2N for counting sort to be slower.  
  
I can't tell how much k and N differ in real-world programs, I guess it differs depending on the application and that there isn't a single answer.

---

## Comment 3

> Username: spreadsort  
> Created at: 2016-05-22 14:46:03 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-220836213  

Hi Morwenn,  
  
On Fri, May 20, 2016 at 7:49 AM Morwenn notifications@github.com wrote:  
  
> The check if (*max - *min <= last - first) ensures that counting sort is  
> called only when k <= N.  
>   
> For example in the test « Shuffled » there are one million elements  
> ranging from 0 to 1000000 while in the test « Shuffled (16 values) » there  
> are one million elements ranging from 0 to 15. Basically, unless when there  
> is an explicit « 16 values », every value is unique, even though the size  
> of the range never exceeds the number of values (except maybe for «  
> Alternating » where it might be k = 2N). Even in spreadsort's best cases,  
> we need to have something akin to k > 2N for counting sort to be slower.  
>   
> I can't tell how much k and N differ in real-world programs, I guess it  
> differs depending on the application and that there isn't a single answer.  
>   
> Spreadsort basically degenerates to counting sort if k <= N, N <  
> 2^max_splits, and int_log_mean_bin_size = 0.  What you have discovered is  
> that spreadsort's tunings are not optimal across all input data  
> distributions and hardware.  In particular, on CPUs with large, fast L2  
> caches (such as Intel server processors), the optimal max_splits may be  
> much larger (as much as 20 or so), depending on the input data  
> distribution.  On the other hand, too large of a max_splits can cause a  
> huge performance degradation on systems with weaker L2 caches (due to cache  
> misses).  I picked a small default max_splits (11) for safety.  
  
You're welcome to try increasing max_splits and (optionally) decreasing  
log_mean_bin_size (in boost/sort/spreadsort/detail/constants.hpp) to see  
how well spreadsort emulates counting sort.  Note that due to a performance  
optimization I made a while back to put bin_sizes on the stack instead of  
the heap, a large max_splits may cause a stack overflow on systems with an  
antiquated stack size limit.  
  
The Boost.Sort distribution has a tune.pl script that is designed to pick  
the optimal tunings for various hardware.  
  
Your graph also suggests that spreadsort should use pdq sort as its  
comparison-based fallback (if somebody can add an implementation to Boost)  
and be retuned to optimize using it.  I'd be happy to integrated pdq sort  
if there were Boost-licensed version.

---

## Comment 4

> Username: Morwenn  
> Created at: 2016-05-22 16:59:46 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-220843286  

Aw, I always forget that there are tuning constants as well as an automated program to generate them. I will try that it report the results when I have time.  
  
Concerning pdqsort, I actually already use it as a fallback for spreadsort in my library (the one used for the benchmarks), and it always improves it compared to using `std::sort` as a fallback. Honestly [pdqsort](https://github.com/orlp/pdqsort) is released under the zlib license, and if I'm not mistaken you are allowed to relicense it under the Boost license. pdqsort is on its way to be integrated in libstdc++ and libc++, but if you ask @orlp, he will probably be glad to help porting the sort to Boost.Sort too so that everybody can benefit from it :)

---

## Comment 5

> Username: orlp  
> Created at: 2016-05-22 17:08:55 UTC  
> Updated at: 2016-05-22 17:09:14 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-220843738  

All my code is available under the zlib license, and could be relicensed under the Boost license. If that's not enough, I hereby also grant explicit permission to relicense it under Boost. If that is still not enough let me know what needs to be done - copyright is no issue here.  
  
On top of the [github repo](https://github.com/orlp/pdqsort) containing the full code + some of the benchmarks I wrote (which @Morwenn modified for the graphs above), I'm also working on [a paper explaining pattern-defeating quicksort](https://drive.google.com/file/d/0B1-vl-dPgKm_T0Fxeno1a0lGT0E/view). It is not ready for publication, but the draft is in an acceptable enough of a state to learn about and discuss pdqsort's inner workings/design decisions. Speling erors and incorrektness may occur - the code in my repo is authorative,

---

## Comment 6

> Username: spreadsort  
> Created at: 2016-05-22 17:33:00 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-220845020  

On Sun, May 22, 2016, 12:59 PM Morwenn notifications@github.com wrote:  
  
> Aw, I always forget that there are tuning constants as well as an  
> automated program to generate them. I will try that it report the results  
> when I have time.  
>   
> Concerning pdqsort, I actually already use it as a fallback for spreadsort  
> in my library (the one used for the benchmarks), and it always improves it  
> compared to using std::sort as a fallback. Honestly pdqsort  
> https://github.com/orlp/pdqsort is released under the zlib license, and  
> if I'm not mistaken you are allowed to relicense it under the Boost  
> license. pdqsort is on its way to be integrated in libstdc++ and libc++,  
> but if you ask @orlp https://github.com/orlp, he will probably be glad  
> to help porting the sort to Boost.Sort too so that everybody can benefit  
> from it :)  
>   
> If it's already in the process of integration with libstdc++, then it's  
> probably best to just wait for that and use it when available.  
> Are any std::sort implementations using it yet or planning to?  
  
Once integrated with pdqsort, the min sort size and log  min split count  
probably should be increased to account for the better speed of pdqsort.  
  
—  
  
> You are receiving this because you commented.  
>   
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/sort/issues/11#issuecomment-220843286

---

## Comment 7

> Username: orlp  
> Created at: 2016-05-22 17:37:51 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-220845273  

It's in the process of _evaluation_ for libstdc++, and the last I've heard of libc++ was a year ago, when they said they were working on a benchmark framework first, but were definitely interested. (Google libcxx/libstdc++ and pdqsort should reveal the relevant mailing list posts).  
  
I have not contacted or have been contacted by anyone in the MSVC team.  
  
I would still consider already implementing pdqsort into Boost.Sort, as progress tends to move slow with these projects, and MSVC is especially uncertain.

---

## Comment 8

> Username: spreadsort  
> Created at: 2016-05-31 10:12:40 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-222647619  

Hi Orson,  
  
First off, pdqsort looks pretty good, and I'd be interested in including it  
in the Boost.Sort library if that's ok with the general boost community.  
If we did that:  
1. I'd need a version of the pqdsort header with the boost license (like  
   in this file  
   https://github.com/boostorg/sort/blob/develop/include/boost/sort/sort.hpp).  
   I am not comfortable modifying the license on a header file; I feel the  
   author should do that.  
2. I'd need the pdqsort header modified to comply with Boost Guidelines  
   http://www.boost.org/development/requirements.html, and put inside the  
   boost::sort namespace (as it's a single header, I'm not whether it needs to  
   be in boost::sort::pdqsort), including renaming it to be a .hpp.  
3. I'd like you to supply documentation to fit into the Boost.Sort  
   library's existing documentation and format.  It can be basic and link to  
   external sources for more complex questions, if you prefer.  
4. I'd send the prospective merger out for a mini-review once it's ready.  
  
Feel free to copy the Boost.Sort repository, edit it to include pdqsort,  
and email me a zipped (or .tar.gz) copy.  That's what we can submit for  
mini-review.  
I tested out pdqsort on the Boost.Sort benchmarks  
https://github.com/boostorg/sort/blob/develop/tune.pl, as a replacement  
for std::sort as a fallback algorithm inside spreadsort (without re-tuning  
the fallback sorting threshold).  The main impact I saw was a massive speed  
improvement for the mostly-sorted case; the case I've had the most user  
complaints about (as spreadsort is only slight faster in this case than  
std::sort with std::sort as a fallback).  
Will you want edit access to the Boost.Sort repository  
https://github.com/boostorg/sort if your sublibrary is accepted?  
  
On Sun, May 22, 2016 at 1:08 PM Orson Peters notifications@github.com  
wrote:  
  
> All my code is available under the zlib license, and could be relicensed  
> under the Boost license. If that's not enough, I hereby also grant explicit  
> permission to relicense it under Boost. If that is still not enough let me  
> know what needs to be done - copyright is no issue here.  
>   
> On top of the github repo https://github.com/orlp/pdqsort containing  
> the full code + some of the benchmarks I wrote (which @Morwenn  
> https://github.com/Morwenn modified for the graphs above), I'm also  
> working on a paper explaining pattern-defeating quicksort  
> https://drive.google.com/file/d/0B1-vl-dPgKm_T0Fxeno1a0lGT0E/view. It  
> is not ready for publication, but the draft is in an acceptable enough of a  
> state to learn about and discuss pdqsort's inner workings/design decisions.  
> Speling erors and incorrektness may occur.  
  
I was reading through your docs and encountered this:  
Where n is the number of elements, and p is the percentile of the pivot  
after partitioning. T(n, 1/2) is the best case for quicksort. On modern  
systems heapsort is profiled to be approximately 1.8 to 2 times as slow as  
quicksort. Choosing p such that T(n, 1/2) / T(n, p) ~= 1.9 as n gets big  
will ensure that we will only switch to heapsort if it would speed up the  
sorting. p = 1/8 is a reasonably close value and is cheap to compute on  
every platform using a bitshift.  
  
With single-pivot random selection, T(n, 1/4) is what you'd expect from  
Quicksort.  with median of 3, the math is trickier, but it's something like  
T(n, 3/8).  It isn't T(n, 1/2).  Still, 1/8 seems reasonable for this  
purpose.  
  
> —  
> You are receiving this because you commented.  
>   
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/sort/issues/11#issuecomment-220843738

---

## Comment 9

> Username: orlp  
> Created at: 2016-05-31 13:09:45 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-222683013  

1. No problem.  
2. I am unfamiliar with these coding standards (note: I've never contributed to Boost before), but I'll try my best. One thing that troubles me is that nothing is mentioned of the style of comments Boost.Sort seems to use that I presume are automatically processed to generate documentation (seems to be doxygen). E.g.: https://github.com/boostorg/sort/blob/develop/include/boost/sort/spreadsort/integer_sort.hpp#L34. I have never used doxygen before, and am unfamiliar with both the formatting and toolchain.  
3. I'm a bit confused as to what the role of pdqsort will play in Boost.Sort, and how to document that. My first impression was that pdqsort would just be an internal fallback case for spreadsort, but your usage of the term 'sublibrary' make it seem as if you intend pdqsort to also be a separate callable part of Boost.Sort visible to end users. I feel that this requires such substantial documentation changes I would not know where to begin and what is expected. Additionally, when reading through the documentation of Boost.Sort there seem to be many instances in which `std::sort` is mentioned as a fall back, and I believe it requires someone more familiar with the project to maintain documentation consistency. I did not even check the example programs, but I assume that a similar concern applies there.  
  
> Will you want edit access to the Boost.Sort repository  
> https://github.com/boostorg/sort if your sublibrary is accepted?  
  
Not without first knowing what my role in the project would be, and what sort of edits would be considered appropriate and/or expected.

---

## Comment 10

> Username: orlp  
> Created at: 2016-05-31 13:27:39 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-222687743  

To follow up on the re-licensing, here is pdqsort's implementation released by me under the boost license: https://gist.github.com/orlp/2c417ab76391b126e1d58bac4bf4af0f  
  
I only changed the bare minimum (prefixed BOOST_ to macros, put `pdqsort` in `boost::sort` and changed the license), but in case I get hit by a bus it should still be possible to modify it further to integrate.

---

## Comment 11

> Username: spreadsort  
> Created at: 2016-06-01 11:01:12 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-222960347  

On Tue, May 31, 2016 at 9:10 AM Orson Peters notifications@github.com  
wrote:  
  
> 1. No problem.  
> 2. I am unfamiliar with these coding standards (note: I've never  
>    contributed to Boost before), but I'll try my best. One thing that troubles  
>    me is that nothing is mentioned of the style of comments Boost.Sort seems  
>    to use that I presume are automatically processed to generate documentation  
>    (seems to be doxygen). E.g.:  
>    https://github.com/boostorg/sort/blob/develop/include/boost/sort/spreadsort/integer_sort.hpp#L34.  
>    I have never used doxygen before, and am unfamiliar with both the  
>    formatting and toolchain.  
> 3. I'm a bit confused as to what the role of pdqsort will play in  
>    Boost.Sort, and how to document that. My first impression was that pdqsort  
>    would just be an internal fallback case for spreadsort, but your usage of  
>    the term 'sublibrary' make it seem as if you intend pdqsort to also be a  
>    separate callable part of Boost.Sort visible to end users. I feel that this  
>    requires such substantial documentation changes I would not know where to  
>    begin and what is expected. Additionally, when reading through the  
>    documentation of Boost.Sort there seem to be many instances in which  
>    std::sort is mentioned as a fall back, and I believe it requires  
>    someone more familiar with the project to maintain documentation  
>    consistency. I did not even check the example programs, but I assume that a  
>    similar concern applies there.  
>   
> Will you want edit access to the Boost.Sort repository  
>   
> https://github.com/boostorg/sort if your sublibrary is accepted?  
>   
> Not without first knowing what my role in the project would be, and what  
> sort of edits would be considered appropriate and/or expected.  
>   
> Thanks for making the license edit.  
> 2) Yes, we use doxygen to generate documentation.  This involves both  
> comments of the type you linked to in my headers for all your exposed API  
> calls (looks like you have 2), in a fairly self-explanatory format, and an  
> edit to the doc/sort.qbk in the Boost.Sort library (create a section like  
> section:integer_sort; you can email it to me), and I'll take care of  
> re-organizing the surrounding documentation.  Paul Bristow converted the  
> original documentation to the current format, so he'd know more about how  
> it works.  
> 3) My plan would be to provide it as an additional library call that's an  
> option in Boost.Sort if that's ok with you, along with calling it as a  
> fallback in spreadsort.  I can replace all my references to std::sort with  
> pdqsort (you don't need to do that), but I want documentation on your  
> library call, even if it's brief.

---

## Comment 12

> Username: orlp  
> Created at: 2016-06-01 11:23:10 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-222964549  

All right, so if I understand correctly I  
- need to change the header to use Boost coding standards  
- add doxygen documentation to `boost::sort::pdqsort(begin, end, comp)` and `boost::sort::pdqsort(begin, end)` and  
- write a _new_ section in `doc/sort.qbk` that adds documentation for pdqsort.  
  
And if I understand correctly, I don't  
- need to add doxygen documentation to every internal call in `pdqsort_detail`  
- modify other header files to refer to pdqsort  
- edit the rest of the documentation/examples to be consistent with pdqsort  
  
Did I understand this correctly?  
  
P.S.: you didn't answer my question regarding edit access.

---

## Comment 13

> Username: spreadsort  
> Created at: 2016-06-01 11:42:19 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-222968115  

On Wed, Jun 1, 2016 at 7:23 AM Orson Peters notifications@github.com  
wrote:  
  
> All right, so if I understand correctly I  
> - need to change the header to use Boost coding standards  
> - add doxygen documentation to boost::sort::pdqsort(begin, end, comp)  
>   and boost::sort::pdqsort(begin, end) and  
> - write a _new_ section in doc/sort.qbk that adds documentation for  
>   pdqsort.  
>   
> Though you don't have to add it to sort.qbk; you can just send the content  
> to me via email or a file, and I'll integrate it in a version of the  
> library we'll use for a mini-review I'll run, and hopefully push to master  
> after the review.  
>   
> And if I understand correctly, I don't  
> - need to add doxygen documentation to every internal call in  
>   pdqsort_detail  
> - modify other header files to refer to pdqsort  
> - edit the rest of the documentation/examples to be consistent with  
>   pdqsort  
>   
> Did I understand this correctly?  
>   
> Yes.  
>   
> P.S.: you didn't answer my question regarding edit access.  
>   
> Wasn't aware it was a question.  Here's my attempt at answering the  
> assumed question:  
> I would expect you to fix any bugs that are reported in pdqsort itself, or  
> in the documentation specifically about it (which you will write).  I hope  
> these are fairly rare, so you may not need edit access, as you could send  
> your edits to me as pull requests.

---

## Comment 14

> Username: orlp  
> Created at: 2016-07-05 10:30:15 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-230443360  

I apologize for the delay. I got distracted with other things, but now something interesting came up.  
  
I was made aware of a new paper on arXiv: [BlockQuicksort: How Branch Mispredictions don't affect Quicksort](http://arxiv.org/abs/1604.06697). I have implemented this in an experimental branch of pdqsort: https://github.com/orlp/pdqsort/tree/block.  
  
I'm still working on benchmarking, tuning and 100% checking it's bug-free, but it's very promising so far.  
  
![block](https://cloud.githubusercontent.com/assets/202547/16581897/248e11b0-42ac-11e6-8fe3-309791774a94.png)  
  
I want to get this finished before integration.

---

## Comment 15

> Username: spreadsort  
> Created at: 2016-07-05 22:58:41 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-230627147  

On Tue, Jul 5, 2016, 6:30 AM Orson Peters notifications@github.com wrote:  
  
> I apologize for the delay. I got distracted with other things, but now  
> something interesting came up.  
>   
> I was made aware of a new paper on arXiv: BlockQuicksort: How Branch  
> Mispredictions don't affect Quicksort http://arxiv.org/abs/1604.06697.  
> I have implemented this in an experimental branch of pdqsort:  
> https://github.com/orlp/pdqsort/tree/block.  
>   
> I'm still working on benchmarking, tuning and 100% checking it's bug-free,  
> but it's very promising so far.  
>   
> [image: block]  
> https://cloud.githubusercontent.com/assets/202547/16581897/248e11b0-42ac-11e6-8fe3-309791774a94.png  
>   
> I want to get this finished before integration.  
>   
> Sounds good.  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/sort/issues/11#issuecomment-230443360, or mute  
> the thread  
> https://github.com/notifications/unsubscribe/AE2j3J6XAND9qEsrxDeGLLfVkwI7YRyVks5qSjI3gaJpZM4IinXK  
> .

---

## Comment 16

> Username: Morwenn  
> Created at: 2016-08-01 14:16:32 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-236592990  

Back to the original topic of this issue: I tried to use tune.pl to actually test spreadsort with optimal tuning, but wasn't able to run the script. My system was unable to find `b2` and since I don't know what `b2`, googling for the name didn't really help to say the least. I'm on Windows in case that matters.

---

## Comment 17

> Username: spreadsort  
> Created at: 2016-08-02 00:19:12 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-236751348  

The README https://github.com/boostorg/sort/blob/develop/README.md  
describes the process.  You need to run bootstrap in the boost root to  
create b2.  You may need to run in an administrator shell to correctly  
create the files and links on Windows.  
  
On Mon, Aug 1, 2016 at 10:16 AM Morwenn notifications@github.com wrote:  
  
> Back to the original topic of this issue: I tried to use tune.pl to  
> actually test spreadsort with optimal tuning, but wasn't able to run the  
> script. My system was unable to find b2 and since I don't know what b2,  
> googling for the name didn't really help to say the least. I'm on Windows  
> in case that matters.  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/sort/issues/11#issuecomment-236592990, or mute  
> the thread  
> https://github.com/notifications/unsubscribe-auth/AE2j3JoUynPb8XlCJ6YtzLxWeQ7YX8xcks5qbf_AgaJpZM4IinXK  
> .

---

## Comment 18

> Username: zamazan4ik  
> Created at: 2016-12-27 10:31:35 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269307836  

What the status of algorithm's implementation? Can i help?

---

## Comment 19

> Username: orlp  
> Created at: 2016-12-28 07:48:44 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269439877  

Pattern-defeating quicksort's implementation is done, and I have integrated the new ideas from BlockQuicksort. This is the most up-to-date performance graph:  
  
![Performance graph](http://i.imgur.com/1RnIGBO.png)  
  
I will work this week on doing what's needed to integrate into Boost.Sort.

---

## Comment 20

> Username: zamazan4ik  
> Created at: 2016-12-28 07:52:13 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269440195  

Great!

---

## Comment 21

> Username: orlp  
> Created at: 2016-12-28 10:01:22 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269454918  

@spreadsort   
  
Alright, I believe I am done. These are the files you can merge: https://gist.github.com/orlp/a344e9e9783b4adf5cc61a26410f0dbd  
  
There are 3 files: pdqsort.hpp, pdqsort.png, sort.qbk.  
  
The first is self-explanatory, the second contains a little bechmark image included by the documentation I've written (read: mostly copy/pasted from my Github repo), and sort.qbk contains the section I've written.  
  
I'm still working on the paper for pattern-defeating quicksort, but when that is done I will notify you so you can add a reference to it somewhere.  
  
If anything is missing, let me know.

---

## Comment 22

> Username: zamazan4ik  
> Created at: 2016-12-28 11:13:50 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269463150  

Also you should prepare test and example before merging. Did you compare pdqsort with timsort?

---

## Comment 23

> Username: Morwenn  
> Created at: 2016-12-28 11:31:05 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269464973  

Considering every benchmark to date, timsort is cute compared to pdqsort :p  
  
Timsort can probably end up as a winner when the comparisons are the real expensive operation since it's been designed for this use case. We'd have to check that.

---

## Comment 24

> Username: spreadsort  
> Created at: 2016-12-30 12:05:56 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269764435  

Timsort is the default sort in some Java implementations; it's good for particular types of problems, especially mostly-sorted data, which is a common situation.  
  
With regards to pdqsort:  I benchmarked replacing std::sort with pdqsort using my tune.pl script (included in the libs/sort dir in the boost release; you may want the [fix I just made to tune.pl](https://github.com/boostorg/sort/blob/master/tune.pl) in the Boost.Sort repository to run it on linux).  I saw substantial speedups on integer and float sorting, but non-trivial slowdowns with string sorting (specifically the stringsort and reversestringsort examples).  You can either investigate those issues and try to fix them, or I can look into just using pdqsort for integer_sort and float_sort.  
Let me know your preference.  
  
Also, did you want pdqsort offered directly as option in Boost.Sort (as your documentation and namespacing suggest), or to just include it as a subsidiary algorithm in spreadsort?  We might need a mini-review to include it as a prominent direct option, though I can check on that if you want.

---

## Comment 25

> Username: orlp  
> Created at: 2016-12-30 14:59:36 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269780830  

> Also, did you want pdqsort offered directly as option in Boost.Sort (as your documentation and namespacing suggest), or to just include it as a subsidiary algorithm in spreadsort? We might need a mini-review to include it as a prominent direct option, though I can check on that if you want.  
  
It would be nice if it was directly offered as an option in Boost.Sort. This is what you also said earlier (assuming the account @spreadsort isn't controlled by multiple people):  
  
> 3) My plan would be to provide it as an additional library call that's an  
> option in Boost.Sort if that's ok with you, along with calling it as a  
> fallback in spreadsort. I can replace all my references to std::sort with  
> pdqsort (you don't need to do that), but I want documentation on your  
> library call, even if it's brief.  
  
I will look into `stringsort` / `reversestringsort` (but only when directly used on pdqsort). Where can I find `input.txt` that the example refers to though?  
  
In the meantime you could just continue integrating only enabling pdqsort for integer/float cases while I track down what is going on for strings.

---

## Comment 26

> Username: orlp  
> Created at: 2016-12-30 15:28:02 UTC  
> Updated at: 2016-12-30 15:28:29 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269784057  

Alright, I believe I have figured out the slowdown for strings. It has nothing to do with the ideas in pattern-defeating quicksort, but rather the ideas of BlockQuicksort I integrated into pattern-defeating quicksort.  
  
I give a very brief description of this technique at https://github.com/orlp/pdqsort#the-average-case.  
  
It seems that (in the way I've implemented it right now), it provides a tremendous speedup (50-80%) for the branchless case, such as comparing integers/floats, but a small but nontrivial slowdown in the case of branches in the comparison operator (~10%), which is the case for strings. I will look into alternative ways of implementing it to eliminate this, but I'm not sure if it's possible.  
  
You should be able to reproduce my findings by replacing `partition_right` with the following definition for strings:  
  
    // Partitions [begin, end) around pivot *begin using comparison function comp. Elements equal  
    // to the pivot are put in the right-hand partition. Returns the position of the pivot after  
    // partitioning and whether the passed sequence already was correctly partitioned. Assumes the  
    // pivot is a median of at least 3 elements and that [begin, end) is at least  
    // insertion_sort_threshold long.  
    template<class Iter, class Compare>  
    inline std::pair<Iter, bool> partition_right(Iter begin, Iter end, Compare comp) {  
        typedef typename std::iterator_traits<Iter>::value_type T;  
          
        // Move pivot into local for speed.  
        T pivot(BOOST_PDQSORT_PREFER_MOVE(*begin));  
  
        Iter first = begin;  
        Iter last = end;  
  
        // Find the first element greater than or equal than the pivot (the median of 3 guarantees  
        // this exists).  
        while (comp(*++first, pivot));  
  
        // Find the first element strictly smaller than the pivot. We have to guard this search if  
        // there was no element before *first.  
        if (first - 1 == begin) while (first < last && !comp(*--last, pivot));  
        else                    while (                !comp(*--last, pivot));  
  
        // If the first pair of elements that should be swapped to partition are the same element,  
        // the passed in sequence already was correctly partitioned.  
        bool already_partitioned = first >= last;  
          
        // Keep swapping pairs of elements that are on the wrong side of the pivot. Previously  
        // swapped pairs guard the searches, which is why the first iteration is special-cased  
        // above.  
        while (first < last) {  
            std::iter_swap(first, last);  
            while (comp(*++first, pivot));  
            while (!comp(*--last, pivot));  
        }  
  
        // Put the pivot in the right place.  
        Iter pivot_pos = first - 1;  
        *begin = BOOST_PDQSORT_PREFER_MOVE(*pivot_pos);  
        *pivot_pos = BOOST_PDQSORT_PREFER_MOVE(pivot);  
  
        return std::make_pair(pivot_pos, already_partitioned);  
    }  
  
This is the old version before I had integrated the BlockQuicksort technique. Could you perform your benchmarks again with this function substituted in? If my hypothesis is correct you should see a significant slowdown of the integer/float case (compared to pdqsort with BlockQuicksort), but `pdqsort` ever so slightly edging out `std::sort` in all cases. The only significant speedups left will be the speedup gained by being pattern-defeating over `std::sort`.  
  
Now I go back into my dungeon and try to see if I can make BlockQuicksort fast even in the presence of branches...

---

## Comment 27

> Username: orlp  
> Created at: 2016-12-30 16:42:50 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269792867  

I've identified a micro-optimization that in my benchmarks puts `pdqsort` within 1-2% (sometimes faster, sometimes slower) of `std::sort` for strings. It involves making the compiler generate less branch-sensitive data-dependent code by reorganizing a section.  
  
Now we do this:  
  
    bool c0 = !comp(*it, pivot); ++it;  
    bool c1 = !comp(*it, pivot); ++it;  
    bool c2 = !comp(*it, pivot); ++it;  
    bool c3 = !comp(*it, pivot); ++it;  
    bool c4 = !comp(*it, pivot); ++it;  
    bool c5 = !comp(*it, pivot); ++it;  
    bool c6 = !comp(*it, pivot); ++it;  
    bool c7 = !comp(*it, pivot); ++it;  
    offsets_l[num_l] = i++; num_l += c0;  
    offsets_l[num_l] = i++; num_l += c1;  
    offsets_l[num_l] = i++; num_l += c2;  
    offsets_l[num_l] = i++; num_l += c3;  
    offsets_l[num_l] = i++; num_l += c4;  
    offsets_l[num_l] = i++; num_l += c5;  
    offsets_l[num_l] = i++; num_l += c6;  
    offsets_l[num_l] = i++; num_l += c7;  
  
instead of:  
  
    offsets_l[num_l] = i++; num_l += !comp(*it, pivot); ++it;  
    offsets_l[num_l] = i++; num_l += !comp(*it, pivot); ++it;  
    offsets_l[num_l] = i++; num_l += !comp(*it, pivot); ++it;  
    offsets_l[num_l] = i++; num_l += !comp(*it, pivot); ++it;  
    offsets_l[num_l] = i++; num_l += !comp(*it, pivot); ++it;  
    offsets_l[num_l] = i++; num_l += !comp(*it, pivot); ++it;  
    offsets_l[num_l] = i++; num_l += !comp(*it, pivot); ++it;  
    offsets_l[num_l] = i++; num_l += !comp(*it, pivot); ++it;  
  
The full code then becomes:  
  
    // Partitions [begin, end) around pivot *begin using comparison function comp. Elements equal  
    // to the pivot are put in the right-hand partition. Returns the position of the pivot after  
    // partitioning and whether the passed sequence already was correctly partitioned. Assumes the  
    // pivot is a median of at least 3 elements and that [begin, end) is at least  
    // insertion_sort_threshold long.  
    template<class Iter, class Compare>  
    inline std::pair<Iter, bool> partition_right(Iter begin, Iter end, Compare comp) {  
        typedef typename std::iterator_traits<Iter>::value_type T;  
  
        // Move pivot into local for speed.  
        T pivot(BOOST_PDQSORT_PREFER_MOVE(*begin));  
        Iter first = begin;  
        Iter last = end;  
  
        // Find the first element greater than or equal than the pivot (the median of 3 guarantees  
        // this exists).  
        while (comp(*++first, pivot));  
  
        // Find the first element strictly smaller than the pivot. We have to guard this search if  
        // there was no element before *first.  
        if (first - 1 == begin) while (first < last && !comp(*--last, pivot));  
        else                    while (                !comp(*--last, pivot));  
  
        // If the first pair of elements that should be swapped to partition are the same element,  
        // the passed in sequence already was correctly partitioned.  
        bool already_partitioned = first >= last;  
        if (!already_partitioned) {  
            std::iter_swap(first, last);  
            ++first;  
        }  
  
        // The following branchless partitioning is derived from "BlockQuicksort: How Branch  
        // Mispredictions don’t affect Quicksort" by Stefan Edelkamp and Armin Weiss.  
        unsigned char offsets_l_storage[block_size + cacheline_size];  
        unsigned char offsets_r_storage[block_size + cacheline_size];  
        unsigned char* offsets_l = align_cacheline(offsets_l_storage);  
        unsigned char* offsets_r = align_cacheline(offsets_r_storage);  
        int num_l, num_r, start_l, start_r;  
        num_l = num_r = start_l = start_r = 0;  
          
        while (last - first > 2 * block_size) {  
            // Fill up offset blocks with elements that are on the wrong side.  
            if (num_l == 0) {  
                start_l = 0;  
                Iter it = first;  
                for (unsigned char i = 0; i < block_size;) {  
                    bool c0 = !comp(*it, pivot); ++it;  
                    bool c1 = !comp(*it, pivot); ++it;  
                    bool c2 = !comp(*it, pivot); ++it;  
                    bool c3 = !comp(*it, pivot); ++it;  
                    bool c4 = !comp(*it, pivot); ++it;  
                    bool c5 = !comp(*it, pivot); ++it;  
                    bool c6 = !comp(*it, pivot); ++it;  
                    bool c7 = !comp(*it, pivot); ++it;  
                    offsets_l[num_l] = i++; num_l += c0;  
                    offsets_l[num_l] = i++; num_l += c1;  
                    offsets_l[num_l] = i++; num_l += c2;  
                    offsets_l[num_l] = i++; num_l += c3;  
                    offsets_l[num_l] = i++; num_l += c4;  
                    offsets_l[num_l] = i++; num_l += c5;  
                    offsets_l[num_l] = i++; num_l += c6;  
                    offsets_l[num_l] = i++; num_l += c7;  
                }  
            }  
            if (num_r == 0) {  
                start_r = 0;  
                Iter it = last;  
                for (unsigned char i = 0; i < block_size;) {  
                    bool c0 = comp(*--it, pivot);  
                    bool c1 = comp(*--it, pivot);  
                    bool c2 = comp(*--it, pivot);  
                    bool c3 = comp(*--it, pivot);  
                    bool c4 = comp(*--it, pivot);  
                    bool c5 = comp(*--it, pivot);  
                    bool c6 = comp(*--it, pivot);  
                    bool c7 = comp(*--it, pivot);  
                    offsets_r[num_r] = ++i; num_r += c0;  
                    offsets_r[num_r] = ++i; num_r += c1;  
                    offsets_r[num_r] = ++i; num_r += c2;  
                    offsets_r[num_r] = ++i; num_r += c3;  
                    offsets_r[num_r] = ++i; num_r += c4;  
                    offsets_r[num_r] = ++i; num_r += c5;  
                    offsets_r[num_r] = ++i; num_r += c6;  
                    offsets_r[num_r] = ++i; num_r += c7;  
                }  
            }  
  
            // Swap elements and update block sizes and first/last boundaries.  
            int num = (std::min)(num_l, num_r);  
            swap_offsets(first, last, offsets_l + start_l, offsets_r + start_r,  
                         num, num_l == num_r);  
            num_l -= num; num_r -= num;  
            start_l += num; start_r += num;  
            if (num_l == 0) first += block_size;  
            if (num_r == 0) last -= block_size;  
        }  
  
        int l_size = 0, r_size = 0;  
        int unknown_left = (last - first) - ((num_r || num_l) ? block_size : 0);  
        if (num_r) {  
            // Handle leftover block by assigning the unknown elements to the other block.  
            l_size = unknown_left;  
            r_size = block_size;  
        } else if (num_l) {  
            l_size = block_size;  
            r_size = unknown_left;  
        } else {  
            // No leftover block, split the unknown elements in two blocks.  
            l_size = unknown_left/2;  
            r_size = unknown_left - l_size;  
        }  
  
        // Fill offset buffers if needed.  
        if (unknown_left && !num_l) {  
            start_l = 0;  
            Iter it = first;  
            for (unsigned char i = 0; i < l_size;) {  
                offsets_l[num_l] = i++; num_l += !comp(*it, pivot); ++it;  
            }  
        }  
        if (unknown_left && !num_r) {  
            start_r = 0;  
            Iter it = last;  
            for (unsigned char i = 0; i < r_size;) {  
                offsets_r[num_r] = ++i; num_r += comp(*--it, pivot);  
            }  
        }  
  
        int num = (std::min)(num_l, num_r);  
        swap_offsets(first, last, offsets_l + start_l, offsets_r + start_r, num, num_l == num_r);  
        num_l -= num; num_r -= num;  
        start_l += num; start_r += num;  
        if (num_l == 0) first += l_size;  
        if (num_r == 0) last -= r_size;  
          
        // We have now fully identified [first, last)'s proper position. Swap the last elements.  
        if (num_l) {  
            offsets_l += start_l;  
            while (num_l--) std::iter_swap(first + offsets_l[num_l], --last);  
            first = last;  
        }  
        if (num_r) {  
            offsets_r += start_r;  
            while (num_r--) std::iter_swap(last - offsets_r[num_r], first), ++first;  
            last = first;  
        }  
  
        // Put the pivot in the right place.  
        Iter pivot_pos = first - 1;  
        *begin = BOOST_PDQSORT_PREFER_MOVE(*pivot_pos);  
        *pivot_pos = BOOST_PDQSORT_PREFER_MOVE(pivot);  
  
        return std::make_pair(pivot_pos, already_partitioned);  
    }  
  
@spreadsort It would be nice if you could also benchmark this version. This should retain the extra speedups for branchless comparison operators while maintaining (almost) full speed for branches.

---

## Comment 28

> Username: spreadsort  
> Created at: 2017-01-01 01:32:06 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269888823  

In my testing that helped speed up reverse_string_sort by 2%, making it only 5% slower than std::sort, but string_sort was still 5% slower and it seemed to hurt other (non-string) times a little.  My inclination is to just skip pdqsort for string_sort, and just put a warning in the documentation about the cases where it might be a bit slower.  
  
For references, this is my system info: 8GB RAM, Intel® Core™ i7-3612QM CPU @ 2.10GHz × 8 , Ubuntu 16.04.1 LTS 64-bit  
  
If all this sounds reasonable to you, I'll test it on large datasets and windows too.

---

## Comment 29

> Username: spreadsort  
> Created at: 2017-01-01 01:32:40 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269888833  

Forgot to mention: I'd prefer to keep your original code, as it is faster in non-string settings in my testing.

---

## Comment 30

> Username: orlp  
> Created at: 2017-01-01 02:07:00 UTC  
> Updated at: 2017-01-01 02:09:24 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269889533  

@spreadsort  I posted two different versions to try out - one older version and a newer version, did you benchmark both? And with `-O2` (in my experience `-O3` is slower)?  
  
There really never should be any reason for the older version to be slower than `std::sort`. At least I can not reproduce it.

---

## Comment 31

> Username: orlp  
> Created at: 2017-01-01 02:20:13 UTC  
> Updated at: 2017-01-01 02:20:48 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269889771  

@spreadsort Also, numbers like 2% and 5% seem dangerously close to measurement error - are you making sure you do multiple trials, long enough benchmarks and letting each implementation 'warm up' in the cache?  
  
P.S.: how exactly do you run your benchmarks, so I could perhaps reproduce and see what's going on?

---

## Comment 32

> Username: spreadsort  
> Created at: 2017-01-01 04:10:59 UTC  
> Updated at: 2017-01-01 04:18:38 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269891661  

I had benchmarked the newer change, which is about 5% slower, consistently (variation of <1% between runs).  
  
I just went back and benchmarked the older version, and it's roughly neutral vs. std::sort, except in some cases with more complex data structure sorting where it looks slightly faster.  
The block operation speedups look substantial, and I'd like to keep them, but could that approach be slower even with integer_sort and float_sort if someone used a more complex data structure like a string?  
  
I find that carefully optimized code tends to perform better on O3 than O2, but that varies depending on the code.  I'm building with the project default, whatever that is.  
Note, I fixed the tune.pl script to refer to ../../b2, instead of just b2.  That should fix the problem you encountered with it.

---

## Comment 33

> Username: orlp  
> Created at: 2017-01-01 15:32:27 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269907308  

@spreadsort The whole crux of the block quicksort is that it's branchless. It's 'straight' code the CPU can march through without ever stalling or undoing a branch mispredict.  
  
If the comparison function (e.g. `std::less` for strings) or the iterators themselves (`std::deque`) have branches, this advantage disappears. Since it also does slightly more work in return for being branchless, this would result in a slight slowdown.  
  
Now in the perfect world there was some magic compiler directive `function_contains_branches` we could use to decide whether to use the block sorting or not.  
  
@spreadsort One option is to include both old versions (block version, and regular), and select the block version when sorting integers or floats, the regular version otherwise.

---

## Comment 34

> Username: spreadsort  
> Created at: 2017-01-01 18:32:35 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269913921  

How about something like this:  
inline typename boost::enable_if_c< std::numeric_limits<  
    typename std::iterator_traits<RandomAccessIter>::value_type >::is_integer  
    || std::numeric_limits<  
    typename std::iterator_traits<RandomAccessIter>::value_type >::is_iec559,  
    void >::type  
just like I use here: https://github.com/boostorg/sort/blob/develop/include/boost/sort/spreadsort/spreadsort.hpp  
  
and only using the block sort if a built-in comparison like std::less (or std::greater) is being used for a built in integer or float type?  Branches are very common in comparisons of anything else.

---

## Comment 35

> Username: orlp  
> Created at: 2017-01-01 20:37:49 UTC  
> Updated at: 2017-01-01 20:40:24 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269918528  

Yeah, something like that.  
  
Although it may also be an option to expose two `pdqsort` calls: `pdqsort` and `pdblockqsort`. `pdqsort` dispatches exactly like you say, but if the user read the documention on `boost::sort::pdqsort` and knows his comparison function is branchless he can call `boost::sort::pdblockqsort`.  
  
A third option is to expose __both__ versions, as `pdqsort_block` and `pdqsort_branch`, and plain `pdqsort` dispatches between the two, but users can choose one over the other if they profiled that to be better on their particular system.

---

## Comment 36

> Username: spreadsort  
> Created at: 2017-01-02 00:43:28 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269926992  

Considering the magnitude of the speed differences I'm seeing, that's a reasonable solution, as long as you document all 3.  
  
I actually see little benefit to pdqsort_branch vs std::sort (inside spreadsort), so I'm inclined to use just pdqsort_block where I know it's safe inside spreadsort (plain integer_sort and float_sort when they use std::less), and use std::sort everywhere else.  
  
This is worth a mini-review, with 3 potential options:  
1) Leave spreadsort as-is  
2) Use pdqsort_block wherever we know for certain the comparison won't branch.  
3) Fully replace std::sort with pdqsort.  
  
And both 2&3 would involve exposing pdqsort as a top-level call.  
  
At this point I'm thinking of option 2, but all will be possibilities in the review.

---

## Comment 37

> Username: orlp  
> Created at: 2017-01-02 02:45:11 UTC  
> Updated at: 2017-01-02 02:46:44 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-269930679  

@spreadsort `pdqsort_branch` and `std::sort` are basically identical, except `pdqsort` becomes much better if there is an exploitable pattern. It's safe to use wherever `std::sort` is used.  
  
But compare `pdqsort_branch` to `std::sort` for the ascending case, descending case, all equal case, 4 different values case as some examples, and you'll see why the algorithm is named as such.  
  
From studying the libstdc++ implementation, there really shouldn't be any scenario where `pdqsort_branch` is measurably worse than `std::sort`.  
  
That being said, I don't know under what circumstances spreadsort calls a fallback sorting algorithm, so it might be possible those patterns never occur.  
  
---  
  
For me personally the prime goal would probably be to offer pattern-defeating quicksort as a sorting algorithm in Boost.Sort so the general public can find and use it. Exactly in what fashion spreadsort would use `pdqsort` internally I think I best leave to you, as you're more familiar with its details than I am.  
  
This is of course me being presumptuous in that pdqsort passes the mini-review. What exactly does this process look like?

---

## Comment 38

> Username: orlp  
> Created at: 2017-01-26 23:46:36 UTC  
> Updated at: 2017-01-26 23:47:12 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-275550018  

Now with the latest commit in https://github.com/orlp/pdqsort I've made it so that `pdqsort` only uses branchless partitioning when:  
  
1. You're using C++11 or higher (for `type_traits`, the Boost port can substitute those).  
2. The comparison operator is `std::less` or `std::greater`.  
3. The type you're sorting is `std::is_arithmetic`.  
  
However, you can always manually request branchless partitioning by calling `pdqsort_branchless` instead of `pdqsort`.

---

## Comment 39

> Username: spreadsort  
> Created at: 2017-01-27 01:08:18 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-275563102  

That sounds good.  I'll tell Francisco, who wants to run the review.

---

## Comment 40

> Username: zamazan4ik  
> Created at: 2017-05-09 18:36:37 UTC  
> Updated at: 2017-05-09 18:37:04 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-300261707  

@spreadsort As I know, Francisco is unavailable now, so... What the current pdqsort status? Can you review it?

---

## Comment 41

> Username: zamazan4ik  
> Created at: 2017-05-18 15:29:01 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-302440422  

@spreadsort Why do you not want to include counting_sort to Boost.Sort? It's a very-very-very good algorithm fo sorting a large set of small values. And any algorithm can't beat counting_sort in this case.

---

## Comment 42

> Username: spreadsort  
> Created at: 2017-05-18 21:13:42 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-302543447  

What's the difference between counting sort and a single iteration of  
spreadsort?  I believe spreadsort is the more general solution.  I'm also  
skeptical of the importance this relatively simple special case.  
  
On Thu, May 18, 2017, 11:29 AM Alexander <notifications@github.com> wrote:  
  
> @spreadsort <https://github.com/spreadsort> Why do you not want to  
> include counting_sort to Boost.Sort? It's a very-very-very good algorithm  
> fo sorting a large set of small values. And any algorithm can't beat  
> counting_sort in this case.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/11#issuecomment-302440422>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AE2j3Of3X5ht7dP4lP0HEC_efLWYzzH3ks5r7GO9gaJpZM4IinXK>  
> .  
>

---

## Comment 43

> Username: Morwenn  
> Created at: 2017-05-19 07:35:00 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-302631560  

I know of two flavours of counting sort:  
* The one described in [ska_sort's description](https://probablydance.com/2016/12/27/i-wrote-a-faster-sorting-algorithm/), which allows to use an `ExtractKey` parameters, which is equivalent to range-v2-style projections.  
* [An integer-specific one](http://stackoverflow.com/a/37105668/1364752) which tends to be extremely fast, but can't handle projections.  
  
If I'm not mistaken, a single iteration of spreadsort would be equivalent to the first of these algorithms. When I augmented spreadsort to use projections in my sorting library, I couldn't use the second counting sort because it does not handle projections. Well, I could dispatch on the `identity` function object to switch to second when possible, but...

---

## Comment 44

> Username: spreadsort  
> Created at: 2017-05-21 10:31:08 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-302928229  

I see; it's basically using the fact that it can just write out the values for the ints that it counted.  Arguably the better solution is to just count the values and skip calling it a sort, just carry around the counts which are more compact in this case.  
Do you know of any real-world problems that spend non-trivial compute resources on this problem?

---

## Comment 45

> Username: Morwenn  
> Created at: 2017-05-21 10:39:58 UTC  
> Url: https://github.com/boostorg/sort/issues/11#issuecomment-302928612  

To be honest, I don't know any real-world problem that spends non-trivial compute resources simply sorting integers (except when sorting a collection of objects on an integer member, which can be done with projections, but then we're not exactly sorting integers anymore) :p
