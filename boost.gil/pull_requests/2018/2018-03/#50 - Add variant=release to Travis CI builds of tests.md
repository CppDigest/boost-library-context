# #50 Add variant=release to Travis CI builds of tests [Merged]

> Username: mloskot  
> Created at: 2018-03-18 14:01:59 UTC  
> Updated at: 2018-04-01 13:54:21 UTC  
> Merged at: 2018-04-01 13:54:16 UTC  
> Closed at: 2018-04-01 13:54:16 UTC  
> Url: https://github.com/boostorg/gil/pull/50  

This aims to confirm there are no issues depending on build configuration (related to #46 and #49).  
  
## References  
  
#51   
  
-----  
  
BTW, `variant=debug,release` is intentional, it first builds tests with `variant=debug`, then `variant=release`.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2018-03-28 00:04:54 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376714954  

The `channel` test fails whenever I compile the "channel.cpp" file with -O3. I'm looking into it now...

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2018-03-28 00:09:28 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376715842  

...in the `test_channel_convert()` function, a call to `channel_convert<>` results in wrong values. This could indeed be a manifestation of the same issue as in https://github.com/boostorg/gil/issues/49.

---

## Comment 3

> Username: chhenning  
> Created_at: 2018-03-28 00:13:06 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376716443  

@stefanseefeld Can you make a minimal code sample?

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2018-03-28 00:31:35 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376719681  

Yes, and I made an interesting observation:  
  
I narrowed the error down to a call to ` do_test<reference_core<bits32&> >().test_all()`.  
As the next step I "unwrapped" that function, replacing it with  
```  
   do_test<reference_core<bits32&> >().test_channel_invert();  
   do_test<reference_core<bits32&> >().test_channel_convert();  
   do_test<reference_core<bits32&> >().test_channel_multiply();  
   do_test<reference_core<bits32&> >().test_channel_math();  
```  
which made the error disappear ! Notice, however, that this isn't strictly equivalent. In the original case above the `test_all()` call reuses the `do_test` object for the four test functions, while in my "unwrapped" case I construct new temporary `do_test` objects for each of those functions.  
Thus suggests that the problem is that the object's state gets altered in a way to make a subsequent test function call fail.  
@chhenning does this ring a bell ? Can you think of any possible reason for that behaviour, which is also specific to this using channel *references*...

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2018-03-28 00:34:38 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376720197  

(You may just drop the above code right into `main()` to reproduce the problem. Don't wrap it in a `try` block, so a failure will cause an "unexpected" exception, allowing you to inspect the core dump...)

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2018-03-28 01:48:00 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376732591  

@chhenning , looking into the `channel.hpp` file, I notice quite a few classes (`packed_channel_reference_base` and all its subclasses) having modifiable state, yet all member functions being declared `const`. What's up with that ? Can you explain the meaning of `const` there ?

---

## Comment 7

> Username: mloskot  
> Created_at: 2018-03-28 08:33:58 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376806144  

I guess, this can be confirmed as a bug

---

## Comment 8

> Username: chhenning  
> Created_at: 2018-03-28 14:30:03 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376907563  

Ok. I have set up visual studio code on ubuntu and with boost gil master the channel.cpp test runs through. I'm using gcc 5.4.something  
  
This would imply that gil develop different gil core which is weird.

---

## Comment 9

> Username: mloskot  
> Created_at: 2018-03-28 14:33:06 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376908584  

@chhenning   
> boost gil master the channel.cpp test runs through. I'm using gcc 5.4.something  
  
What compilation flags exactly?

---

## Comment 10

> Username: chhenning  
> Created_at: 2018-03-28 14:43:18 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376912297  

Yeah, I was just testing debug configuration.

---

## Comment 11

> Username: chhenning  
> Created_at: 2018-03-28 14:44:16 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376912667  

On gil develop I can see an exception with release and release_with_debug_symbols. But no exception with debug config.

---

## Comment 12

> Username: mloskot  
> Created_at: 2018-03-28 14:52:08 UTC  
> Updated_at: 2018-03-28 14:53:32 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376915528  

@chhenning That seems to confirm the issue.  
  
To summary:  
  
- It occurs only for optimised builds only on Linux+**GCC**, #51, https://travis-ci.org/boostorg/gil/jobs/355009262  
  
- It does NOT happen with Linux+**clang**, https://travis-ci.org/boostorg/gil/jobs/355009263  
  
**Question**: does it occur for optimised builds for both, 32bit and 64bit targets or only for 64bit, as it interestingly does in case of the checksum bug, #49, https://ci.appveyor.com/project/stefanseefeld/gil/build/1.0.182-develop

---

## Comment 13

> Username: stefanseefeld  
> Created_at: 2018-03-28 16:09:38 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376943615  

It would be useful to pay close attention to the compiler warnings. (Disable those about unused local typedefs to reduce the noise.) There are a few from `channel.hpp` about using uninitialized memory. I didn't quite understand that warning, it looked bogus. But then, it may hint at the problem.  
(Inserting print statements to print out the `max` and `min` values in the test makes the failure disappear, so I'm pretty sure this has to do with illegally aliased data that the optimization pass may actually elide, or some such. I haven't looked at `clang`s warnings yet.

---

## Comment 14

> Username: mloskot  
> Created_at: 2018-03-28 16:20:14 UTC  
> Updated_at: 2018-03-28 16:27:01 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376947115  

Good findings with outputting the intermediate `min/max` values.  
  
@stefanseefeld @chhenning It may be a good time to start shooting down the warnings.  
  
To avoid chasing our own and each others tails, we rather get somewhat systematic about it.  
  
Let's agree on some plan:  
  
1. Create `fix-warnings` shared branch in the `boostorg/gil`  
2. Temporarily disable the failing tests  
3. Compile using clang, gcc, msvc (`/W3` level but perhaps `/W4` too)  
4. Pick warning, fix it, commit   
5. Repeat 2-3 until we are are warnings-free and the builds are green  
6. Re-enable failing tests and see what happens.  
7. If this does not help, we run static analysis and repeat the clean-up cycles.  
  
Comments?

---

## Comment 15

> Username: chhenning  
> Created_at: 2018-03-28 17:19:01 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376966131  

Here is my minimal reproducible code  
```  
#include <iostream>  
#include <boost/gil/gil_all.hpp>  
  
using namespace boost::gil;  
using namespace std;  
  
void error_if(bool condition) {  
    if (condition)  
        throw std::exception();  
}  
  
bits32s c32s_min = channel_traits<bits32s>::min_value();  
bits32s c32s_max = channel_traits<bits32s>::max_value();  
  
// For channel values simply initialize the value directly  
template <typename ChannelValue>  
struct value_core {  
    typedef ChannelValue channel_t;  
    channel_t _min_v, _max_v;  
  
    value_core() : _min_v(channel_traits<ChannelValue>::min_value()), _max_v(channel_traits<ChannelValue>::max_value())   
    {}  
};  
  
int main(int argc, char *argv[])  
{  
    value_core<bits32s> a;  
  
    bits32s v_min, v_max;  
  
    v_min = channel_convert<bits32s>(c32s_min);  
    v_max = channel_convert<bits32s>(c32s_max);  
  
    //std::cout << v_min << std::endl;  
    // std::cout << this->_min_v << std::endl;  
    //std::cout << v_max << std::endl;  
    // std::cout << this->_max_v << std::endl;  
  
    error_if(v_min != a._min_v || v_max != a._max_v);    
}  
```

---

## Comment 16

> Username: chhenning  
> Created_at: 2018-03-28 17:19:42 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376966346  

Funny enough, when enabling the cout's. All is good.

---

## Comment 17

> Username: stefanseefeld  
> Created_at: 2018-03-28 17:24:10 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376967774  

Yes, the parallels to https://github.com/boostorg/gil/issues/51 are remarkable: The fact that the error occurs only when the optimizer is switched on, and that one can suppress it simply by referencing certain variables explicitly (e.g. printing them) suggests there is "undefined behaviour" at play. Read: the code plays tricks (probably with aliasing) that aren't allowed according to the standard, and which get into the compiler's way when optimizing the code.

---

## Comment 18

> Username: chhenning  
> Created_at: 2018-03-28 17:31:11 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376969982  

To expedite the issue I sent an email to boost mailing list.

---

## Comment 19

> Username: mloskot  
> Created_at: 2018-03-28 18:01:52 UTC  
> Updated_at: 2018-03-28 18:03:54 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376979966  

I've read the post on ML, I'd insist we do need better coverage of compilers and configs on CI builds, plus static analysis. Sorry, we can't save energy there, unless we have too much time.  
  
So the bug is pin pointed and warnings clean up not necessary for now?

---

## Comment 20

> Username: stefanseefeld  
> Created_at: 2018-03-28 18:03:45 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376980547  

I have never argued against better coverage. I'v been arguing against redundancy (building the same variants on multiple CI systems, for example, or building unnecessarily often).

---

## Comment 21

> Username: mloskot  
> Created_at: 2018-03-28 18:07:30 UTC  
> Updated_at: 2018-03-28 18:48:45 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-376981736  

I'm sorry if I misunderstood. I'm now on street. I'll be back to this later tonight.   
  
-----  
  
@stefanseefeld My apologies for the misunderstanding once again. The redundancy of builds is certainly bad and inefficient use of the resources. I'll work towards better coverage of complementary builds.

---

## Comment 22

> Username: stefanseefeld  
> Created_at: 2018-03-28 19:07:11 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-377001060  

@mloskot , following a suggestion from the Boost ML, I tried out this patch:  
```  
--- a/include/boost/gil/channel_algorithm.hpp  
+++ b/include/boost/gil/channel_algorithm.hpp  
@@ -329,7 +329,7 @@ template <> struct channel_convert_to_unsigned<bits16s> : public std::unary_func  
   
 template <> struct channel_convert_to_unsigned<bits32s> : public std::unary_function<bits32s,bits32> {  
     typedef bits32 type;  
-    type operator()(bits32s x) const { return static_cast<bits32>(x+(1<<31)); }  
+    type operator()(bits32s x) const { return static_cast<bits32>(x)+(1<<31); }  
 };  
```  
  
which works for me. Can you try that in your branch (i.e., this PR) ?

---

## Review 23 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-03-28 19:12:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/50#pullrequestreview-107812900  

Just a small nit-pick: I'd suggest to separate the different variant builds into distinct commands, just so it becomes easier from browsing the log to identify any error condition.

---

## Comment 24

> Username: mloskot  
> Created_at: 2018-03-28 19:39:16 UTC  
> Updated_at: 2018-03-28 21:01:46 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-377010360  

@stefanseefeld @chhenning I've just updated the PR with the UB fix cead84ee29c9b4eb904eaff337eba2a0b4672005

---

## Comment 25

> Username: mloskot  
> Created_at: 2018-03-28 21:20:16 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-377040796  

I've reverted 9d9fc09a8ffb39667bc27625285d2d8337ba4034 that temporarily disabled the failing checksum test cases and updated this PR to see if the UB fix also fixes the checksum bug #49

---

## Comment 26

> Username: mloskot  
> Created_at: 2018-03-28 23:06:45 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-377067070  

I've copied the fix to more places, see #68

---

## Comment 27

> Username: mloskot  
> Created_at: 2018-04-01 13:54:11 UTC  
> Url: https://github.com/boostorg/gil/pull/50#issuecomment-377788456  

I've updated the build jobs layout adding multiple compilers: GCC 5, 6, 7 and clang 3.9 X `variant=release,debug`. I defined `variant=release` as allowed to failure builds for now, until we solve the checksum/channel bugs.

---
