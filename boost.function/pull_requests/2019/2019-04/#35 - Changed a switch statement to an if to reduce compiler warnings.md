# #35 Changed a switch statement to an if to reduce compiler warnings [Merged]

> Username: anadon  
> Created at: 2019-04-25 17:17:56 UTC  
> Updated at: 2019-04-30 14:33:16 UTC  
> Merged at: 2019-04-30 14:33:16 UTC  
> Closed at: 2019-04-30 14:33:16 UTC  
> Url: https://github.com/boostorg/function/pull/35  

Just to reduce compiler warnings.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2019-04-25 17:31:48 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-486766639  

AMDG  
  
Which warnings on which compilers?  The original code  
looks correct and doesn't seem to be doing anything  
even remotely dodgy.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: anadon  
> Created_at: 2019-04-25 17:37:04 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-486768401  

It triggers on `-Wswitch-enum`.  Tested on gcc 8.3.

---

## Comment 3

> Username: swatanabe  
> Created_at: 2019-04-25 17:55:32 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-486774999  

AMDG  
  
On 4/25/19 11:37 AM, jrmarsha wrote:  
> It triggers on `-Wswitch-enum`.  Tested on gcc 8.3.  
>   
  
Well don't use -Wswitch-enum, then.  I am strongly  
against working around this be any method other  
than suppression.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: anadon  
> Created_at: 2019-04-25 18:07:53 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-486779555  

I disagree, and here is why.  Let's make a pro-con list of this kind of trivial change, taking into account the perspective of a user in addition to Boost and the compiler.  
  
+fewer compiler warnings  
~no change in correctness  
~no change in performance  
~no change in understandability  
+makes leveraging the compiler warning less burdensome  
  
There aren't really any negatives.  There may be a perceived negative for changing something to change it, but that's not really a problem.  But if someone wants to use the `-Wswitch-enum` in their project, who are we to make that more burdensome or full of clutter?  Working with compiler warnings like this is a virtue for users.

---

## Comment 5

> Username: jeking3  
> Created_at: 2019-04-26 17:22:28 UTC  
> Updated_at: 2019-04-26 17:25:11 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-487134958  

Reading through the documentation for this optional warning, I understand the intention is to help identify cases where one adds to an enumeration and then does not update every switch statement, leaving the defaults, which *could be* incorrect (although I would suggest comprehensive unit testing would mostly eliminate this concern).    
  
I can suggest a compromise here, as one way to deal with this without rewriting it is to put all the remaining enumerations into the same case group as default.  That causes no changes to the logic at all, and makes it more friendly to this type of defensive warning.  Unless the default build jobs run with `-Wswitch-enum` and `-Werror` (which can be very difficult to achieve) though, nobody would notice if it ever broke again.  
  
Still not sure it's really necessary though.  Perhaps a better approach is to find a way to disable the warning with pragmas around the code.

---

## Comment 6

> Username: anadon  
> Created_at: 2019-04-26 19:10:37 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-487168920  

I'd argue that those alternatives are likely to be larger changes which do not provide as clean of an outcome.  There is no need or utility in keeping the older code as it was, but there is some utility in the simple change to an if to catch a single special case.

---

## Comment 7

> Username: swatanabe  
> Created_at: 2019-04-26 22:33:39 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-487221325  

AMDG  
  
This warning is completely insane.  Treating it as  
a hard rule essentially amounts to "never use default"  
which is in no way reasonable.  Anyone one who uses  
it should expect to see a lot of false positives and  
have some way of dealing with them (i.e. -isystem)  
  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored -Wswitch-enum  
...  
#pragma GCC diagnostic pop  
  
(It can go at the top of the file along with  
the msvc suppression)  
  
In Christ,  
Steven Watanabe

---

## Comment 8

> Username: anadon  
> Created_at: 2019-04-26 22:56:47 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-487225763  

That's really dismissive, and the pragma alternative is more complex than just structuring to avoid the warning.  It isn't worth the effort to just ignore or disable a warning.  It isn't that bad.

---

## Comment 9

> Username: swatanabe  
> Created_at: 2019-04-26 23:45:55 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-487233299  

AMDG  
  
On 4/26/19 4:56 PM, jrmarsha wrote:  
> That's really dismissive,  
  
Yep.  
  
For bonus points: -Wswitch-enum -Wswitch-default  
  
> and the pragma alternative is more complex than just structuring to avoid the warning.  
  
The pragma is better because it's completely separate  
from the main code.  
  
>  It isn't worth the effort to just ignore or disable a warning.  It isn't that bad.  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 10

> Username: pdimov  
> Created_at: 2019-04-26 23:58:42 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-487234967  

I can't reproduce this warning. On what code does it trigger?

---

## Comment 11

> Username: anadon  
> Created_at: 2019-04-27 04:01:29 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-487252493  

I was fixing some depreciation warnings, and figured I'd turn up the warning levels just to check that everything was squeaky clean.  Back from when I was asking you about adding compiler flags, actually.  This popped up from graph tests from that, traced it back here, looked small so I changed it to what is in the diff, tested, then made the PR.

---

## Comment 12

> Username: pdimov  
> Created_at: 2019-04-27 04:18:41 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-487253247  

OK, but what code reproduces the warning?

---

## Comment 13

> Username: anadon  
> Created_at: 2019-04-28 04:11:14 UTC  
> Url: https://github.com/boostorg/function/pull/35#issuecomment-487341648  

@pdimov Full details here: https://gist.github.com/anadon/bca4cb245ba452682cb669d5d51e2f78  
  
The parts of interest are lines 41-58, 68-85, 188-203, 818-834, 1385-1401, 1956-1972, 1988-2003, corresponding to `all_planar_input_files_test.cpp`, `parallel_edges_loops_test.cpp`, `mas_test.cpp`, `stoer_wagner_test.cpp`, `successive_shortest_path_nonnegative_weights_test.cpp`, `cycle_canceling_test.cpp`, `find_flow_cost_bundled_properties_and_named_params_test.cpp`, `max_flow_algorithms_bundled_properties_and_named_params.cpp`.

---

## Review 14 [Commented]

> Username: pdimov  
> Created_at: 2019-04-29 16:45:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/function/pull/35#pullrequestreview-231783727  

📁 include/boost/function/function_base.hpp

```diff
 510 |-           switch (op) {
 511 |-           case get_functor_type_tag:
 510 |+           if(op == get_functor_type_tag){
```

> Username: pdimov  
> Created_at: 2019-04-29 16:45:33 UTC  
> Updated_at: 2019-04-29 19:05:24 UTC  
> Url: https://github.com/boostorg/function/pull/35#discussion_r279445531  

Please observe the existing whitespace conventions in this file; `if (...) {`, `} else {`.


---
