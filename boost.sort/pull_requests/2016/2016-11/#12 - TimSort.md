# #12 TimSort [Open]

> Username: zamazan4ik  
> Created at: 2016-11-28 13:51:55 UTC  
> Updated at: 2017-06-10 10:39:02 UTC  
> Url: https://github.com/boostorg/sort/pull/12  

This is initial version of TimSort. Can you do review, please?

---

## Review 1 [Commented]

> Username: spreadsort  
> Created_at: 2016-12-27 02:26:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/sort/pull/12#pullrequestreview-14423005  

The source looks ok.  
  
What's the Jamfile change about?  
  
We'll need tests, a benchmark, (can be simple), and a mini-review of the code before we can submit this.  It'll also be necessary to add this to the documentation (in quickbooks) but that should be fairly simple with just one library call.  
  
My thought is that once you have the tests + benchmark, we can run a mini-review, but should wait on merging it in until the parallel library has finished merging.

---

## Comment 2

> Username: zamazan4ik  
> Created_at: 2016-12-27 10:10:36 UTC  
> Updated_at: 2016-12-28 06:43:55 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-269305456  

Ok. I will add tests, benchmarks, etc. in a few days.

---

## Review 3 [Commented]

> Username: spreadsort  
> Created_at: 2017-01-01 03:26:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/sort/pull/12#pullrequestreview-14809808  

📁 include/boost/sort/spreadsort/string_sort.hpp

```diff
 428 |+ \par
 429 | Worst-case performance is <em>  O(N * (lg(range)/s + s)) </em>,
 430 | so @c integer_sort is asymptotically faster
```

> Username: spreadsort  
> Created_at: 2017-01-01 03:26:26 UTC  
> Updated_at: 2017-01-12 01:50:11 UTC  
> Url: https://github.com/boostorg/sort/pull/12#discussion_r94279760  

Thanks for catching the integer_sort references, but you missed one here.


---

## Review 4 [Commented]

> Username: spreadsort  
> Created_at: 2017-01-10 03:16:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/sort/pull/12#pullrequestreview-15843224  

I get compile errors when I grab this.  Does this use C++11, and if so, is it really necessary?  Not everyone has a C++11 compiler yet (I can build C++11, but deliberately use the default b2 for testing).  
  
gcc.compile.c++ ../../bin.v2/libs/sort/test/timsort.test/gcc-5.4.0/debug/timsort_test.o  
In file included from test/timsort_test.cpp:9:0:  
../../boost/sort/timsort.hpp:45:7: error: using template type parameter ‘Compare’ after ‘class’  
 class Compare  
       ^  
../../boost/sort/timsort.hpp:97:13: error: ‘Compare’ is not a template  
     typedef Compare<const value_t &, Compare> compare_t;  
             ^  
../../boost/sort/timsort.hpp: In static member function ‘static void boost::sort::TimSort<RandomAccessIterator, Compare>::binarySort(boo  
st::sort::TimSort<RandomAccessIterator, Compare>::iter_t, boost::sort::TimSort<RandomAccessIterator, Compare>::iter_t, boost::sort::TimS  
ort<RandomAccessIterator, Compare>::iter_t, boost::sort::TimSort<RandomAccessIterator, Compare>::compare_t)’:  
../../boost/sort/timsort.hpp:170:39: error: ‘move’ is not a member of ‘std’  
             /*const*/ value_t pivot = std::move(*start);  
                                       ^  
../../boost/sort/timsort.hpp:175:22: error: ‘move’ is not a member of ‘std’  
                 *p = std::move(*(p - 1));

---

## Comment 5

> Username: zamazan4ik  
> Created_at: 2017-01-12 01:51:38 UTC  
> Updated_at: 2017-01-12 01:53:20 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-272053221  

Thank you for catching this. My bad, sorry for this stupid mistake.  
  
Currently we can compile timsort.hpp with -std=c++03 and -std=c++98.

---

## Review 6 [Commented]

> Username: spreadsort  
> Created_at: 2017-01-13 19:14:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/sort/pull/12#pullrequestreview-16631642  

Thanks for the fixes.  
  
The mostly-sorted benchmark numbers were really impressive!  It's 10+X faster than even pdqsort in that case.  It's slower in the random input case, as is expected.  
  
It'd be interesting to write a chunk-aware spreadsort based on what timsort takes advantage of, but I don't have the time.  
  
Let me know when you're ready for a mini-review, and a 10-day time period when you can answer all questions about timsort promptly, and I'll try to get this on the review schedule.  Note: I will expect you to maintain the timsort portion of the library and be responsive to email after the library is submitted.

---

## Comment 7

> Username: zamazan4ik  
> Created_at: 2017-01-13 20:02:40 UTC  
> Updated_at: 2017-01-14 13:57:15 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-272533919  

Ok. Thank you for your help.  
  
Currently i have a lot of work, so i think will be great, if we will start review in 2-3 weeks (e.g. on 10 February). I will be ready to answer all questions about TimSort. What do you think about this idea?  
  
 All can talk with me by GitHub (most oreferred way), e-mail (zamazan4ik@tut.by), or IRC #boost (nickname: zamazan4ik, zamazan4ik_). My timezone: UTC +3 (Belarus, Minsk).

---

## Comment 8

> Username: spreadsort  
> Created_at: 2017-01-25 01:06:35 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-274989901  

I'm still in discussions with Francisco, who wrote the comparison-based half of the Boost.Sort library that he's in the process of integrating into the whole.  He is the logical person to run the mini-review if he has the time.  Once that's sorted out we'll figure out when is best for a mini review and whether he requires more changes first.

---

## Comment 9

> Username: zamazan4ik  
> Created_at: 2017-01-25 13:37:32 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-275109373  

Ok. Important information: on 1-12 February i will be at hospital, so i haven't good possibilities to participate in mini-review. After 12 February i will be ready.

---

## Comment 10

> Username: zamazan4ik  
> Created_at: 2017-03-13 15:46:59 UTC  
> Updated_at: 2017-03-13 15:47:09 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-286148477  

Hello. What is the status of the review?

---

## Comment 11

> Username: spreadsort  
> Created_at: 2017-03-14 04:49:32 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-286321421  

On Mon, Mar 13, 2017 at 8:47 AM Alexander <notifications@github.com> wrote:  
  
> What is the status of the review?  
>  
  
Francisco (my new co-author as of late last year) wanted to review Timsort,  
and found that it didn't perform quite as well as he expected but still  
provides benefit if the vast majority of the array is sorted.  We're still  
sorting out whether he will review it or I will review it; I am giving him  
the option to do so if he wants.  
  
  
> —  
> You are receiving this because you commented.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/pull/12#issuecomment-286148477>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AE2j3MmqL8cGL0PKRN_FmI9ENTz6hQQOks5rlWTzgaJpZM4K9zQW>  
> .  
>

---

## Comment 12

> Username: zamazan4ik  
> Created_at: 2017-03-14 07:26:40 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-286342363  

Thank you a lot :)

---

## Comment 13

> Username: spreadsort  
> Created_at: 2017-04-02 03:11:09 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-290961702  

I added your range wrapper code changes to develop (including the attribution you added and the documentation error corrections), as those aren't controversial.  I still haven't heard back from Francisco, and if he doesn't get back I'll run the review myself.

---

## Comment 14

> Username: zamazan4ik  
> Created_at: 2017-05-09 18:21:47 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-300257402  

Hello @spreadsort . What the review status?

---

## Comment 15

> Username: spreadsort  
> Created_at: 2017-06-03 00:10:43 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-305935567  

The review is starting tomorrow.  I've emailed you and the boost list about it.  Please be responsive to questions.

---

## Comment 16

> Username: zamazan4ik  
> Created_at: 2017-06-07 03:26:34 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-306675975  

I collected some useful links for potential reviewers:  
1) [Wiki](https://en.wikipedia.org/wiki/Timsort)  
2) [brief description in python dev list](http://svn.python.org/projects/python/trunk/Objects/listsort.txt)  
3) [Original implementation](https://github.com/gfx/cpp-TimSort)

---

## Comment 17

> Username: spreadsort  
> Created_at: 2017-06-07 10:55:48 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-306760851  

Please answer the questions on the boost list directly.

---

## Comment 18

> Username: spreadsort  
> Created_at: 2017-06-10 10:39:02 UTC  
> Url: https://github.com/boostorg/sort/pull/12#issuecomment-307557262  

My email is spreadsort@gmail.com.  Please email me directly.  If I'm to accept your source, I need to be able to contact you to discuss it and have the assurance that you'll fix bugs in it.

---
