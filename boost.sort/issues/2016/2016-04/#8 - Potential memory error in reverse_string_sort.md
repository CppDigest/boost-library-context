# #8 - Potential memory error in reverse_string_sort [Closed]

> Username: Morwenn  
> Created at: 2016-04-01 19:57:17 UTC  
> Updated at: 2016-04-19 02:54:32 UTC  
> Closed at: 2016-04-19 02:54:32 UTC  
> Url: https://github.com/boostorg/sort/issues/8  

As you might know, I work with a modified version of your spreadsort, by I believe that it is semantically equivalent, I just replaced the `boost::` things with equivalent `std::` entities without altering how the algorithm works in any way. I was toying with GCC and Clang's sanitizers when clang++'s address sanitizer reported what looks like a memory error when running `reverse_string_sort`. Once I strip the several abstraction layers, the faulty test looks roughly like this:  
  
```  
std::mt19937_64 engine(std::time(nullptr));  
  
std::vector<std::string> vec;  
for (int i = 0 ; i < 100'000 ; ++i)  
{  
    vec.push_back(std::to_string(i));  
}  
  
std::shuffle(std::begin(vec), std::end(vec), engine);  
boost::sort::spreadsort::reverse_string_sort(std::begin(vec), std::end(vec), std::greater<>{});  
assert(std::is_sorted(std::begin(vec), std::end(vec), std::greater<>{}));  
```  
  
Unfortunately, I randomize the vector based on an `std::time` seed, so I don't know the exact sequence that triggered the error; I tried to reproduce it didn't fire, so it probably depends on the input. I am trying to reproduce it again, meanwhile you can find [the relevant error log](https://travis-ci.org/Morwenn/cpp-sort/jobs/120149826). Since the seed uses `std::time`, it might be possible to find it again using the build time (to be honest, I think it's close to impossible though). I will update this post if I can deterministically reproduce the crash.  
  
---  
  
Ok, I managed to get a seed for `std::mt19937_64` that triggers the memory error with the example above: 1459547468 (or 1459547467 or 1459547469 if I don't read my tests correctly, but I think I do). Just so that things are complete, you can still have the [full error log](https://travis-ci.org/Morwenn/cpp-sort/jobs/120154968), which is pretty much equivalent to the previous one.

---

## Comment 1

> Username: spreadsort  
> Created at: 2016-04-05 03:06:04 UTC  
> Url: https://github.com/boostorg/sort/issues/8#issuecomment-205620523  

On Fri, Apr 1, 2016, 3:57 PM Morwenn notifications@github.com wrote:  
  
> As you might know, I w  
  
ork with a modified version of your spreadsort, by I believe that it is  
  
> semantically equivalent, I just replaced the boost:: things with  
> equivalent std:: entities without altering how the algorithm works in any  
> way. I was toying with GCC and Clang's sanitizers when clang++'s address  
> sanitizer reported what looks like a memory error when running  
> reverse_string_sort. Once I strip the several abstraction layers, the  
> faulty test looks roughly like this:  
>   
> std::mt19937_64 engine(std::time(nullptr));  
>   
> std::vectorstd::string vec;  
> for (int i = 0 ; i < 100'000 ; ++i)  
> {  
>     vec.push_back(std::to_string(i));  
> }  
>   
> std::shuffle(std::begin(vec), std::end(vec), engine);  
> boost::sort::spreadsort::reverse_string_sort(std::begin(vec), std::end(vec), std::greater<>{});  
> assert(std::is_sorted(std::begin(vec), std::end(vec), std::greater<>{}));  
>   
> Unfortunately, I randomize the vector based on an std::time seed, so I  
> don't know the exact sequence that triggered the error; I tried to  
> reproduce it didn't fire, so it probably depends on the input. I am trying  
> to reproduce it again, meanwhile you can find the relevant error log  
> https://travis-ci.org/Morwenn/cpp-sort/jobs/120149826. Since the seed  
> uses std::time, it might be possible to find it again using the build  
> time (to be honest, I think it's close to impossible though). I will update  
> this post if I can deterministically reproduce the crash.  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/sort/issues/8  
>   
> Thanks.  I'll get to this next week.

---

## Comment 2

> Username: spreadsort  
> Created at: 2016-04-13 01:39:48 UTC  
> Url: https://github.com/boostorg/sort/issues/8#issuecomment-209184378  

On Fri, Apr 1, 2016 at 3:57 PM Morwenn notifications@github.com wrote:  
  
> As you might know, I work with a modified version of your spreadsort, by I  
> believe that it is semantically equivalent, I just replaced the boost::  
> things with equivalent std:: entities without altering how the algorithm  
> works in any way. I was toying with GCC and Clang's sanitizers when  
> clang++'s address sanitizer reported what looks like a memory error when  
> running reverse_string_sort. Once I strip the several abstraction layers,  
> the faulty test looks roughly like this:  
>   
> std::mt19937_64 engine(std::time(nullptr));  
>   
> std::vectorstd::string vec;  
> for (int i = 0 ; i < 100'000 ; ++i)  
> {  
>     vec.push_back(std::to_string(i));  
> }  
>   
> std::shuffle(std::begin(vec), std::end(vec), engine);  
> boost::sort::spreadsort::reverse_string_sort(std::begin(vec), std::end(vec), std::greater<>{});  
> assert(std::is_sorted(std::begin(vec), std::end(vec), std::greater<>{}));  
>   
> Unfortunately, I randomize the vector based on an std::time seed, so I  
> don't know the exact sequence that triggered the error; I tried to  
> reproduce it didn't fire, so it probably depends on the input. I am trying  
> to reproduce it again, meanwhile you can find the relevant error log  
> https://travis-ci.org/Morwenn/cpp-sort/jobs/120149826. Since the seed  
> uses std::time, it might be possible to find it again using the build  
> time (to be honest, I think it's close to impossible though). I will update  
> this post if I can deterministically reproduce the crash.  
>   
> Thanks for the error log.  Those line numbers are incorrect; could you  
> email me the string_sort.hpp you're using?  
  
I looked at the memcmp logic, and it makes sure that the string is big  
enough before comparing it.

---

## Comment 3

> Username: Morwenn  
> Created at: 2016-04-13 06:51:15 UTC  
> Updated at: 2016-04-14 17:42:11 UTC  
> Url: https://github.com/boostorg/sort/issues/8#issuecomment-209259549  

Sure, here it is: https://github.com/Morwenn/cpp-sort/blob/master/include/cpp-sort/detail/spreadsort/detail/float_sort.h  
  
Also, if it can help, I just got the same error with g++'s address sanitizer too. Here is the dump (no seed to provide this time, sorry): https://travis-ci.org/Morwenn/cpp-sort/jobs/123119566

---

## Comment 4

> Username: spreadsort  
> Created at: 2016-04-15 03:21:15 UTC  
> Url: https://github.com/boostorg/sort/issues/8#issuecomment-210268665  

On Wed, Apr 13, 2016 at 2:51 AM Morwenn notifications@github.com wrote:  
  
> Sure, here it is:  
> https://github.com/Morwenn/cpp-sort/blob/master/include/cpp-sort/detail/spreadsort/detail/float_sort.h  
>   
> Thanks.  Good catch.  Here's the fix:  
> https://github.com/boostorg/sort/commit/c02e2924c3c403f4e527f92276d8ee962dbbf64c  
  
It's checked into develop, but I think it would be easiest for you to just  
copy.  
It was a simple oversight, and the net impact is the update_offset function  
will run memcmp on invalid addresses for values that don't need it, and  
thus may artificially fail and suppress the common-prefix optimization for  
reverse_string_sort.  It won't impact the sort order at all.  I checked the  
other related calls and they don't have this bug.  
  
> —  
> You are receiving this because you commented.  
>   
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/sort/issues/8#issuecomment-209259549

---

## Comment 5

> Username: Morwenn  
> Created at: 2016-04-15 18:17:19 UTC  
> Url: https://github.com/boostorg/sort/issues/8#issuecomment-210573085  

Thanks, everything runs smoothly now, and no tool finds problems anymore, be it Valgrind, the undefined behaviour sanitizer or the address sanitizer! :)

---

## Comment 6

> Username: spreadsort  
> Created at: 2016-04-19 02:54:32 UTC  
> Url: https://github.com/boostorg/sort/issues/8#issuecomment-211703857  

Thanks for confirming the fix.
