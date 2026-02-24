# #32 Fix warnings reported by clang [Merged]

> Username: jeking3  
> Created at: 2017-11-03 16:20:34 UTC  
> Updated at: 2018-01-03 16:01:38 UTC  
> Merged at: 2018-01-03 16:01:38 UTC  
> Closed at: 2018-01-03 16:01:38 UTC  
> Url: https://github.com/boostorg/random/pull/32  

In building Boost.Uuid I was getting the following warnings with clang:  
```  
clang-linux.compile.c++.without-pth ../../bin.v2/libs/uuid/test/test_random_generator.test/clang-gnu-linux-5.0.0/debug/threadapi-pthread/test_random_generator.o  
In file included from test/test_random_generator.cpp:16:  
In file included from ../../boost/random.hpp:60:  
../../boost/random/binomial_distribution.hpp:407:9: error: anonymous types declared in an anonymous union are an extension [-Werror,-Wnested-anon-types]  
        struct {  
        ^  
In file included from test/test_random_generator.cpp:16:  
In file included from ../../boost/random.hpp:63:  
../../boost/random/discrete_distribution.hpp:95:60: error: unused parameter 'average' [-Werror,-Wunused-parameter]  
    static WeightType normalize(WeightType val, WeightType average)  
                                                           ^  
../../boost/random/discrete_distribution.hpp:186:66: error: unused parameter 'weights' [-Werror,-Wunused-parameter]  
    static WeightType try_get_sum(const std::vector<WeightType>& weights)  
                                                                 ^  
In file included from test/test_random_generator.cpp:16:  
In file included from ../../boost/random.hpp:72:  
In file included from ../../boost/random/negative_binomial_distribution.hpp:21:  
../../boost/random/poisson_distribution.hpp:338:9: error: anonymous types declared in an anonymous union are an extension [-Werror,-Wnested-anon-types]  
        struct {  
        ^  
```  
  
This fixes #25

---

## Comment 1

> Username: swatanabe  
> Created_at: 2017-11-03 18:03:28 UTC  
> Url: https://github.com/boostorg/random/pull/32#issuecomment-341782500  

AMDG  
  
On 11/03/2017 10:20 AM, James E. King, III wrote:  
> In building Boost.Uuid I was getting the following warnings with clang:  
> ```  
> clang-linux.compile.c++.without-pth ../../bin.v2/libs/uuid/test/test_random_generator.test/clang-gnu-linux-5.0.0/debug/threadapi-pthread/test_random_generator.o  
> In file included from test/test_random_generator.cpp:16:  
> In file included from ../../boost/random.hpp:60:  
> ../../boost/random/binomial_distribution.hpp:407:9: error: anonymous types declared in an anonymous union are an extension [-Werror,-Wnested-anon-types]  
>         struct {  
>         ^  
  
  I thought I fixed this.  Apparently not.  Since this  
is a question of standard compliance, it should be  
fixed by making it compliant instead of suppressing  
the warning.  
  
> In file included from test/test_random_generator.cpp:16:  
> In file included from ../../boost/random.hpp:63:  
> ../../boost/random/discrete_distribution.hpp:95:60: error: unused parameter 'average' [-Werror,-Wunused-parameter]  
>     static WeightType normalize(WeightType val, WeightType average)  
  
  Personally, I prefer commenting out the variable name, but  
deleting it works, too.  
  
>     M test/concepts.hpp (2)  
> -    unsigned long long z;  
> +    uint64_t z;  
>   
  
  This change is wrong for C++11 and later (where  
long long is not an extension).  The standard  
specifies that the argument is unsigned long long,  
which I do not believe is guaranteed to be the same  
type as uint64_t.  Also, for the sake of consistency,  
it should be qualified with boost::.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-11-03 18:19:25 UTC  
> Url: https://github.com/boostorg/random/pull/32#issuecomment-341786717  

Okay, I started doing that, but I was touching many lines of code and I was concerned that the review comment would be to disable the warning instead, but I can do the other.  I can comment out the variable name instead of removing it.  I'll look at the third issue again.

---

## Comment 3

> Username: swatanabe  
> Created_at: 2017-11-03 19:08:45 UTC  
> Url: https://github.com/boostorg/random/pull/32#issuecomment-341799155  

AMDG  
  
On 11/03/2017 12:19 PM, James E. King, III wrote:  
> Okay, I started doing that, but I was touching many lines of code and I was concerned that the review comment would be to disable the warning instead, but I can do the other.  
  
  I generally prefer to suppress warnings when the  
change is non-trivial, but that only applies when  
the original code is correct.  
  
For reference: [class.union]  
"The member-specification of an anonymous union shall only define  
non-static data members. [ Note: Nested types and functions cannot  
be declared within an anonymous union. —end note ]"  
  
>  I can comment out the variable name instead of removing it.  
  
  It's not really important.  I only mentioned it since  
I had other comments already.  
  
>  I'll look at the third issue again.  
>   
  
  On second thought, the implementation of the engines  
consistently uses uintmax_t, so that's probably the  
right choice here.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-11-09 19:00:31 UTC  
> Url: https://github.com/boostorg/random/pull/32#issuecomment-343256414  

Do you think the logic in try_get_sum is correct?  It takes a vector of WeightType and returns 1 instead of summing them.

---

## Comment 5

> Username: swatanabe  
> Created_at: 2017-11-09 19:27:55 UTC  
> Url: https://github.com/boostorg/random/pull/32#issuecomment-343264722  

AMDG  
  
On 11/09/2017 12:00 PM, James E. King, III wrote:  
> Do you think the logic in try_get_sum is correct?  It takes a vector of WeightType and returns 1 instead of summing them.  
>   
  
  Yes, it's correct.  For floating point weights, the  
weights are normalized so that their sum is 1.  The  
argument of try_get_sum is presumed to have been  
passed though normalize.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: jeking3  
> Created_at: 2017-11-09 19:40:09 UTC  
> Url: https://github.com/boostorg/random/pull/32#issuecomment-343268384  

This builds locally; I didn't run it through CI.  Should really get the CI PR merged and then enable CI builds for all future pull requests...

---

## Comment 7

> Username: swatanabe  
> Created_at: 2017-11-09 20:01:25 UTC  
> Url: https://github.com/boostorg/random/pull/32#issuecomment-343274416  

AMDG  
  
On 11/09/2017 12:40 PM, James E. King, III wrote:  
> This builds locally; I didn't run it through CI.  Should really get the CI PR merged and then enable CI builds for all future pull requests...  
>   
  
  The #pragma warning suppression is no longer needed.  
Otherwise, it looks good.  (For the record, it  
probably would have been less work for you to  
move the struct definition out of the anonymous  
union instead of giving the union a name.)  
  
In Christ,  
Steven Watanabe

---

## Comment 8

> Username: jeking3  
> Created_at: 2017-11-09 21:21:48 UTC  
> Url: https://github.com/boostorg/random/pull/32#issuecomment-343295522  

Yes, I see what you mean.  If you want me to do it that way that's fine.  I forgot to remove the suppressions because I did the work off the branch and then merged it in.

---

## Comment 9

> Username: jeking3  
> Created_at: 2017-12-05 16:23:33 UTC  
> Url: https://github.com/boostorg/random/pull/32#issuecomment-349357257  

I found a couple more extremely (100+ lines each) verbose warnings for unused variables that needed to be silenced while building this branch today.

---
