# #57 C++11 Variadic Templates based sequences [Closed]

> Username: Flast  
> Created at: 2015-03-03 04:35:12 UTC  
> Updated at: 2015-07-02 16:55:27 UTC  
> Closed at: 2015-06-21 02:43:08 UTC  
> Url: https://github.com/boostorg/fusion/pull/57  

I started discussing about following problem within this feature.  
  
Note: It is too late for 1.58 release, but for 1.59 release because master branch will close within a couple of days.  
  
---  
  
**Updated: this compatibility issue has been resolved.**  
  
About backward compatibilities with user supplied trailing `fusion::void_`, In those commits, especially `fusion::vector`, constructor of `Sequence<T, U, void_>` have a form `Sequence<T, U, void_>::Sequence(T&&, U&&, void_&&)` and user cannot construct it from T and U:  
  
```  
T t;  
U u;  
Sequence<T, U, void_> seq(t, u); // error: too few arguments  
```  
  
Yes, it **doesn't** affect major cases: not documented usage.  
However, Spirit will do. https://github.com/boostorg/spirit/blob/master/include/boost/spirit/home/support/terminal.hpp#L239-L244  
  
Should I continue to support trailing `fusion::void_`?  
Any comments and suggestions are welcomed.

---

## Comment 1

> Username: Flast  
> Created_at: 2015-06-15 17:09:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#issuecomment-112141580  

OK, now I implemented backward compatibility with `vectorN<...>` and `vector<..., void_...>` (only for vector).  
And Spirit's regression test has passed (spirit and spirit/repository).  
  
Now ready to review and merge.

---

## Comment 2

> Username: Flast  
> Created_at: 2015-06-15 17:15:05 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#issuecomment-112142929  

Note, I know this is quite dirty implementation, but I wanna be tested with Boost.Regression earlier than usual and ensure enough durations to eliminate regressions.

---

## Comment 3

> Username: djowel  
> Created_at: 2015-06-15 22:15:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#issuecomment-112226162  

The use of fusion::_void is undocumented. It is OK to stop supporting trailing _void if we make sure all uses are fixed in spirit (or elsewhere).

---

## Comment 4

> Username: djowel  
> Created_at: 2015-06-15 22:18:13 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#issuecomment-112226664  

This is a big commit. It will be hard to review all 163 files. Is it because of the generated PP files?

---

## Comment 5

> Username: djowel  
> Created_at: 2015-06-15 22:34:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#issuecomment-112229478  

it seems the PP files we're refactored and moved to a cpp03 directory, but that obscures the PR for the purpose of review. i'm not sure how to deal with this situation.

---

## Comment 6

> Username: Flast  
> Created_at: 2015-06-15 23:28:46 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#issuecomment-112237090  

OK, I'll open a PR, **just move into cpp03**. It might reduce terrible cpp03 diffs.

---

## Comment 7

> Username: Flast  
> Created_at: 2015-06-17 00:47:52 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#issuecomment-112615005  

OK, now this PR is clearer (and better for reviewing) than original one.

---

## Comment 8

> Username: vtnerd  
> Created_at: 2015-06-17 01:00:49 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32587847  

Should perfect forwarding be added, or does that deviate too much from the original documentation? The one difference is that it would allow mutable l-values, which cannot be currently passed to these functions.

---

## Comment 9

> Username: Flast  
> Created_at: 2015-06-17 01:16:19 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32588486  

@vtnerd , Good catch this. There is no reason to be not universal references (of course provide failback using `BOOST_NO_CXX11_RVALUE_REFERENCES`)

---

## Comment 10

> Username: vtnerd  
> Created_at: 2015-06-17 01:17:32 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32588528  

@djowel said previously:  
  
> The use of fusion::_void is undocumented. It is OK to stop supporting trailing _void if we make sure all uses are fixed in spirit (or elsewhere).  
  
If something in spirit needs to be done to remove this compatibility mode, I can do it if you lack the time. My thinking is that this compatibility mode requires recursive instantations, and it will be less work at compile time if this function is left to a simple variadic expansion like the `make_list` code above. This is probably something for a future commit though - do you know of other Boost projects that fail if this logic isn't performed?

---

## Comment 11

> Username: djowel  
> Created_at: 2015-06-17 01:20:40 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32588656  

Agreed!

---

## Comment 12

> Username: djowel  
> Created_at: 2015-06-17 01:22:41 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32588722  

No, none that I know. Only spirit. The reason is historical: fusion was a spirit sub-library before it became a full fledged boost lib.

---

## Comment 13

> Username: djowel  
> Created_at: 2015-06-17 01:23:10 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32588741  

Feel free to do what is needed. A future commit sounds good.

---

## Comment 14

> Username: djowel  
> Created_at: 2015-06-17 01:26:37 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32588880  

We should do more rvalues handling soon, perhaps in another commit.

---

## Comment 15

> Username: Flast  
> Created_at: 2015-06-17 01:28:22 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32588949  

I agreed future commit (before 1.59 release is perfect) too.

---

## Comment 16

> Username: vtnerd  
> Created_at: 2015-06-17 01:33:42 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32589135  

Is [this problem](https://akrzemi1.wordpress.com/2013/10/10/too-perfect-forwarding/) an issue here? The only difference are the variadics.

---

## Comment 17

> Username: vtnerd  
> Created_at: 2015-06-17 01:50:52 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32589744  

Is [`mpl::size_t`](http://www.boost.org/doc/libs/1_58_0/libs/mpl/doc/refmanual/size-t.html) more appropriate here? Or is there some convention for `mpl::int_`?

---

## Comment 18

> Username: Flast  
> Created_at: 2015-06-17 01:54:04 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32589860  

No. Imagine following case.  
  
``` c++  
// If vector has vector(T const&...) style ctor,  
quite_big_type obj;  
vector<quite_big_type> v(std::move(obj)); // always copy obj  
  
non_copyable_type obj;  
vector<non_copyable_type> v(std::move(obj)); // error  
```  
  
Yes, by-value semantics work well, but ...  
  
``` c++  
// If vector has vector(T...) style ctor,  
non_copyable_type obj;  
vector<non_copyable_type, void_> v(std::move(obj)); // error, ctor needs 2 arguments.  
```  
  
In earlier discussion, trailing void_ will be unsupported, but it is required currently.

---

## Comment 19

> Username: Flast  
> Created_at: 2015-06-17 02:01:33 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32590097  

Preprocessed vector uses `mpl::int_`, and also relating to https://svn.boost.org/trac/boost/ticket/7304 .

---

## Comment 20

> Username: vtnerd  
> Created_at: 2015-06-17 02:15:18 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32590600  

I meant this case:  
  
``` c++  
vector<int> vec = 10;  
vector<int> vec_copy = vec;  
```  
  
I just realized that you handled the overload issue in the base classes. However, the copy and move constructor are identical to this function, so they should be collapsable into this function. Something to consider for the assignment operators in this class too.

---

## Comment 21

> Username: vtnerd  
> Created_at: 2015-06-17 02:44:39 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32591525  

I'm not sure how difficult this would be to change, but you might want to consider multi-inheritance expansion instead of recursive tail expansion inheritance.  
  
About a month ago on IRC, K-ballo pointed out [this blog post](http://www.elbeno.com/blog/?p=1104) incorrectly described the "lambda trick" mentioned on the boost spirit mailing list. The `std::tuple` code in the blog post was slower to compile than the lambda version because of the libstdc++ implementation of `std::tuple`. When compiling the same code with `libc++`, I got the `std::tuple` version to to compile faster than the lambda version (clang 3.5 in c++11 mode on OSX).  
  
libc++ expands the tuple using multiple inheritance, and libstdc++ uses recursive inheritance (similar to this implementation). When using multiple inheritance, larger tuples don't hit the recursive template expansion limit, since the expansion occurs at the same depth level. This instantation difference (and possibly other factors) enable it to compile much faster. You'd have to use an integer sequence that gets unpacked at the same time, but it should be doable. I'm not sure how difficult this is to change in this implementation (I don't expect it being too difficult), but perhaps this is something to profile/change later. Actually, I'll try to make the changes and compare the differences tomorrow, regardless of the status of this patch.

---

## Comment 22

> Username: Flast  
> Created_at: 2015-06-17 11:45:52 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32618049  

Ah, understood.

---

## Comment 23

> Username: Flast  
> Created_at: 2015-06-17 11:47:21 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32618123  

> libc++ expands the tuple using multiple inheritance, and libstdc++ uses recursive inheritance (similar to this implementation). When using multiple inheritance, larger tuples don't hit the recursive template expansion limit, since the expansion occurs at the same depth level.   
  
OK, I'll take a look the implementation.

---

## Comment 24

> Username: vtnerd  
> Created_at: 2015-06-17 18:56:35 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32661716  

The Hana library has a similar implementation that might be easier to read. Tuple inherits from [`closure`](https://github.com/ldionne/hana/blob/master/include/boost/hana/detail/closure.hpp), which contains the relevant components.

---

## Comment 25

> Username: Flast  
> Created_at: 2015-06-18 05:37:17 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32700226  

Multiple inheritance is definitely greater (and readable) implementation than recursive inheritance. I didn't know I can write `...` at _base-clause_. I'll rewrite the implementation.  
@vtnerd , Thank you tell me the trick!

---

## Comment 26

> Username: Flast  
> Created_at: 2015-06-18 16:16:21 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32748117  

Ah, preprocessed version also doesn't support move semantics. So in this PR, still don't support move semantics. Is this OK?  
(It seems that `set` don't support move too.)

---

## Comment 27

> Username: vtnerd  
> Created_at: 2015-06-18 17:13:21 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32753602  

Is there a desire to add boost::move support for C++03 compilers?

---

## Comment 28

> Username: Flast  
> Created_at: 2015-06-18 17:40:36 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32756601  

No, I meant [list](https://github.com/boostorg/fusion/blob/ddcd3cdf93d7d3abb96c093897f52ea07ab21ee3/include/boost/fusion/container/list/detail/cpp03/list.hpp) doesn't check `BOOST_NO_CXX11_RVALUE_REFERENCES` like [this](https://github.com/boostorg/fusion/blob/ddcd3cdf93d7d3abb96c093897f52ea07ab21ee3/include/boost/fusion/container/vector/detail/cpp03/vector.hpp#L159-L187).

---

## Comment 29

> Username: djowel  
> Created_at: 2015-06-18 22:27:51 UTC  
> Updated_at: 2015-06-20 06:43:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32785681  

It's OK for now. But we should work on having all support move soon.

---

## Comment 30

> Username: Flast  
> Created_at: 2015-06-20 06:46:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#issuecomment-113716949  

I reimplemented a fusion::vector with multiple-inheritance. Could you review this?

---

## Comment 31

> Username: djowel  
> Created_at: 2015-06-20 08:42:59 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#discussion_r32883415  

What is this commented out code?

---

## Comment 32

> Username: djowel  
> Created_at: 2015-06-20 08:49:27 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#issuecomment-113732769  

Kohei, this is fascinating work! However, it is still difficult to review. Is it not possible to break this into multiple PRs? Maybe one per container? That way, we can proceed faster.

---

## Comment 33

> Username: Flast  
> Created_at: 2015-06-21 02:43:08 UTC  
> Url: https://github.com/boostorg/fusion/pull/57#issuecomment-113855837  

Okay, I'll.

---
