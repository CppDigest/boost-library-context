# #814 basic_parser_impl: make sentinel() return a unique pointer [Merged]

> Username: MaxKellermann  
> Created at: 2022-11-21 14:01:19 UTC  
> Updated at: 2023-10-05 08:58:42 UTC  
> Merged at: 2022-12-30 19:44:51 UTC  
> Closed at: 2022-12-30 19:44:52 UTC  
> Url: https://github.com/boostorg/json/pull/814  

Right now, sentinel() casts the `basic_parser_impl` pointer (`this`) to `const char *`, but that pointer is not unique if the input buffer happens to be placed right before the `basic_parser_impl` instance - the end of that buffer then has the same address as `basic_parser_impl`.  
  
Example code:  
  
```  
  struct {  
    char buffer[8]{"{\"12345\""};  
    boost::json::stream_parser p;  
  } s;  
  s.p.write(s.buffer, sizeof(s.buffer));  
  s.p.write(":0}", 3);  
```  
  
This stops parsing at the end of the buffer, and then the `incomplete()` check in `parse_string()` will return true; the second `write()` call will crash with assertion failure:  
  
>  boost/json/basic_parser_impl.hpp:1016: const char* boost::json::basic_parser<Handler>::parse_unescaped(const char*, std::integral_constant<bool, StackEmpty_>, std::integral_constant<bool, AllowComments_>, bool) [with bool StackEmpty_ = true; bool IsKey_ = true; Handler = boost::json::detail::handler]: Assertion `*cs == '\x22'' failed.  
  
This changes `sentinel()` to return the address of a static variable instead, which is guaranteed to be unique.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-11-21 14:32:09 UTC  
> Updated_at: 2022-12-30 14:48:43 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1322150533  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/814?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#814](https://codecov.io/gh/boostorg/json/pull/814?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0f987f0) into [develop](https://codecov.io/gh/boostorg/json/commit/6af3a5e05ce8127d04458f60f5e9d5aabf969d21?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6af3a5e) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/814/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/814?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #814   +/-   ##  
========================================  
  Coverage    99.00%   99.00%             
========================================  
  Files           71       71             
  Lines         6832     6832             
========================================  
  Hits          6764     6764             
  Misses          68       68             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/814?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser\_impl.hpp](https://codecov.io/gh/boostorg/json/pull/814/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.33% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/814?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/814?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6af3a5e...0f987f0](https://codecov.io/gh/boostorg/json/pull/814?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2022-11-21 14:32:12 UTC  
> Updated_at: 2022-11-21 14:32:23 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1322150602  

Very interesting change :) @sdkrystian what do you think about this?

---

## Comment 3

> Username: grisumbras  
> Created_at: 2022-11-21 15:26:32 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1322233354  

This should work, but may affect performance. I wanted to see automatic benchmark results. @MaxKellermann also please add a test that's failing without your change (use one commit).

---

## Comment 4

> Username: grisumbras  
> Created_at: 2022-11-21 15:29:23 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1322237389  

Btw, alternative implementation would be to return the address of any parser's member except the first.

---

## Comment 5

> Username: MaxKellermann  
> Created_at: 2022-11-21 15:42:16 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1322256875  

> Btw, alternative implementation would be to return the address of any parser's member except the first.  
  
That's fragile because it can break when you reorder the members. But you could add `+1` to the char-casted `this` pointer.

---

## Comment 6

> Username: MaxKellermann  
> Created_at: 2022-11-21 15:59:19 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1322281254  

I added a failing unit test and I changed the implementation to return `(char*)this + 1`; this was necessary because my first approach was still faulty - theoretically (though unlikely), this global variable could be the end of a global buffer, leading to the same crash as before.

---

## Comment 7

> Username: grisumbras  
> Created_at: 2022-11-21 16:28:01 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1322331328  

this + 1 will end up within the handler. So, I wonder if someone may try feeding the parser with something within the handler.

---

## Comment 8

> Username: MaxKellermann  
> Created_at: 2022-11-21 16:33:18 UTC  
> Updated_at: 2022-11-21 16:33:55 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1322337868  

> this + 1 will end up within the handler  
  
True; but note that without this PR, it was already within the handler, so this PR is an improvement for sure.  
  
What about using `nullptr` as sentinel? That would be cheaper than comparing pointers with `this` (i.e. with a CPU register). Are there corner cases where a valid non-sentinel value can be `nullptr`? (This idea is so obvious that I thought there must be a reason why this wasn't already done.)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-11-21 17:09:44 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1322389725  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/814/pullrequest-condensed-01345d8.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/814/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/814/pullrequest.html)

---

## Comment 10

> Username: grisumbras  
> Created_at: 2022-11-22 05:59:38 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1323129882  

Can you please squash the commits?

---

## Comment 11

> Username: MaxKellermann  
> Created_at: 2022-11-22 08:53:51 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1323317134  

> Can you please squash the commits?  
  
Done.

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-11-22 10:09:38 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1323414845  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/814/pullrequest-condensed-9940b33.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/814/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/814/pullrequest.html)

---

## Comment 13

> Username: sdkrystian  
> Created_at: 2022-11-22 21:04:10 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1324236386  

@vinniefalco this reason why `sentinel` returned `this` was because it would be already loaded in some register when the function was called, so it could simply be moved into `rax` via a register-to-register move (which don''t require any execution resources on modern CPUs). So while this fix may work, it may just be better to return `nullptr` (which I believe should work; possibly even better than the old implementation).

---

## Comment 14

> Username: vinniefalco  
> Created_at: 2022-11-22 21:11:00 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1324242224  

Interesting!!

---

## Comment 15

> Username: MaxKellermann  
> Created_at: 2022-11-22 21:14:04 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1324244854  

> it may just be better to return `nullptr` (which I believe should work; possibly even better than the old implementation).  
  
[I had the same idea yesterday](https://github.com/boostorg/json/pull/814#issuecomment-1322337868), but  
  
> This idea is so obvious that I thought there must be a reason why this wasn't already done  
  
If you confirm there isn't any reason, I'll happily change this PR to `nullptr`!

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2022-11-23 15:46:18 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1325283934  

try `nullptr`

---

## Comment 17

> Username: MaxKellermann  
> Created_at: 2022-11-23 15:52:54 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1325293039  

> try `nullptr`  
  
That fails **lots** of unit tests:  
```  
====== BEGIN OUTPUT ======  
boost.json.parse  
#3 parse.cpp(49) failed: ! ec  
#6 parse.cpp(49) failed: ! ec  
#9 parse.cpp(49) failed: ! ec  
#12 parse.cpp(49) failed: ! ec  
#15 parse.cpp(49) failed: ! ec  
#18 parse.cpp(49) failed: ! ec  
terminate called after throwing an instance of 'boost::wrapexcept<boost::system::system_error>'  
  what():  incomplete JSON [boost.json:3 at ../../boost/json/basic_parser_impl.hpp:2753 in function 'write_some']  
Aborted (core dumped)  
  
EXIT STATUS: 134  
====== END OUTPUT ======  
====== BEGIN OUTPUT ======  
boost.json.value  
#1023 value.cpp(2283) failed: jv == 23  
#1024 value.cpp(2288) failed: jv == 23  
#1026 value.cpp(2294) failed: jv == 23  
#1028 value.cpp(2300) failed: jv == 23  
#1030 value.cpp(2307) failed: jv == 23  
#1033 value.cpp(2315) failed: jv == 23  
3ms, 1 suites, 6 failures, 1034 total.  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
... and many more. I did not investigate further.

---

## Comment 18

> Username: vinniefalco  
> Created_at: 2022-11-23 16:17:01 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1325328212  

Another possibility is to privately derive `basic_parser_impl` from a struct that has a single private char in it:  
```  
class detail::pad  
{  
  char c__ = 0;  
};  
```

---

## Comment 19

> Username: MaxKellermann  
> Created_at: 2022-11-23 16:18:33 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1325330412  

> Another possibility is to privately derive `basic_parser_impl` from a struct that has a single private char in it:  
  
You'd need to have at least two chars, and return a pointer to the second one, to ensure that no preceding buffer can end at the returned pointer, which is the whole problem here.

---

## Comment 20

> Username: vinniefalco  
> Created_at: 2022-11-23 17:10:02 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1325403787  

> You'd need to have at least two chars, and return a pointer to the second one, to ensure that no preceding buffer can end at the returned pointer, which is the whole problem here.  
  
Well no, because `sentinel()` is a member of `basic_parser_impl` so `this` would refer to the `basic_parser_impl` part and not the `detail::pad` part.  
  
```  
struct pad  
{  
  char c;  
};  
  
struct parser : private pad  
{  
  char const* sentinel() const noexcept  
  {  
    return this; // one past `pad`  
  }  
};  
```

---

## Comment 21

> Username: MaxKellermann  
> Created_at: 2022-11-23 18:13:32 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1325474734  

> Well no, because `sentinel()` is a member of `basic_parser_impl` so `this` would refer to the `basic_parser_impl` part and not the `detail::pad` part.  
  
They have the same address in your example (most likely, but that may depend on the compiler-specific C++ ABI).  
  
> one past `pad`  
  
No. Let me godbolt this for you :-) https://godbolt.org/z/63T8c4vx8

---

## Comment 22

> Username: vinniefalco  
> Created_at: 2022-11-23 23:38:34 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1325772911  

:( That is rather unfortunate

---

## Comment 23

> Username: MaxKellermann  
> Created_at: 2022-11-24 09:27:37 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1326178504  

If you want it watertight, I can change this PR to add a `char sentinel[2]` field and return `&sentinel[1]`. Do you want that?  
For my taste, the current state or the PR is "good enough", but it's not (strictly) watertight.  
I'd love to have `nullptr`, but that may require fixing some existing quirks - if you want that, I can give that a try.  
Let me know your preference.

---

## Comment 24

> Username: grisumbras  
> Created_at: 2022-11-24 16:54:05 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1326679977  

How would that be better than just returning an address to any member? Or this + 1?

---

## Comment 25

> Username: MaxKellermann  
> Created_at: 2022-11-24 17:02:50 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1326688167  

> How would that be better than just returning an address to any member?  
  
If you return the address of any member, it's only watertight if you can guarantee that this member is not directly preceded by a buffer to be parsed. You can look at the current layout of the class and speculate it's not, but that's fragile; if eventually the code gets refactored and members get reordered (which is a perfectly reasonable thing to do), this assumption may no longer be true and the code may break.  
  
Having a `char sentinel[2]` and using `&sentinel[1]` guarantees that the pointer is never preceded by a buffer, because `&sentinel[1]` is guaranteed to be preceded by `&sentinel[0]`, no matter which C++ ABI, and no matter how you reorder fields. This is the strictly watertight solution.  
  
> Or this + 1?  
  
It was you who wrote: "this + 1 will end up within the handler. So, I wonder if someone may try feeding the parser with something within the handler."  
It's not whatertight. It's only safe against parsing buffers outside of the parser object, but not against buffers which happen to be inside.

---

## Comment 26

> Username: vinniefalco  
> Created_at: 2022-11-24 18:45:42 UTC  
> Updated_at: 2022-11-24 18:45:57 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1326769943  

`( reinterpret_cast<char const*>(this) + 1 )`

---

## Review 27 [Commented]

> Username: WPMGPRoSToTeMa  
> Created_at: 2022-11-24 19:01:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/814#pullrequestreview-1193540826  

📁 include/boost/json/basic_parser_impl.hpp

```diff
 220 |+     // the "+1" ensures that the returned pointer is unique even if
 221 |+     // the given input buffer borders on this object
 222 |     return reinterpret_cast<
```

> Username: WPMGPRoSToTeMa  
> Created_at: 2022-11-24 19:01:25 UTC  
> Updated_at: 2022-11-24 19:01:26 UTC  
> Url: https://github.com/boostorg/json/pull/814#discussion_r1031778119  

`static_assert(sizeof(*this) > 1);`

> Username: MaxKellermann  
> Created_at: 2022-11-25 12:39:47 UTC  
> Updated_at: 2022-11-25 12:39:48 UTC  
> Url: https://github.com/boostorg/json/pull/814#discussion_r1032417277  

`class basic_parser` has lots of fields, and is always larger than 1 byte, unless this class gets refactored and all fields get removed, which isn't going to happen. I don't think this `static_assert` adds real value, it's trivial to see it's always true.

> Username: vinniefalco  
> Created_at: 2022-11-25 17:25:22 UTC  
> Url: https://github.com/boostorg/json/pull/814#discussion_r1032618786  

Agreed, lol


---

## Comment 28

> Username: grisumbras  
> Created_at: 2022-11-28 12:25:48 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1328991301  

The worst part of making `sentinel()` return `nullptr` is that it will put a precondition on `write_some` (you wouldn't be able to do `parser.write_some(true, nullptr, 0, ec)`. This isn't necessarily bad, but when GCC started optimising around a similar precondition in `memcpy`, it broke a lot of code.   
  
I would stick to returning a pointer into parser, even if that means some reduction in performance. I also don't like adding unnecessary fields just for this purpose, I would prefer returning a pointer to an existing field. We already keep layout of `value` et al. fixed, doing something similar for `basic_parser` shouldn't be a problem.  
  
On the other hand, I'm not necessarily against returning `this + 1`, but we might want to document the fact that people shouldn't feed to the parser buffers inside `handler`.  
  
@vinniefalco your thoughts?

---

## Comment 29

> Username: grisumbras  
> Created_at: 2022-12-30 12:30:55 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1367895089  

@MaxKellermann can you please rebase on current develop and re-push. I will merge it then.

---

## Comment 30

> Username: MaxKellermann  
> Created_at: 2022-12-30 14:19:30 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1367943960  

> @MaxKellermann can you please rebase on current develop and re-push. I will merge it then.  
  
done

---

## Comment 31

> Username: cppalliance-bot  
> Created_at: 2022-12-30 15:34:38 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1367974757  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/814/pullrequest-condensed-8d766b0.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/814/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/814/pullrequest.html)

---

## Comment 32

> Username: grisumbras  
> Created_at: 2022-12-30 19:45:30 UTC  
> Url: https://github.com/boostorg/json/pull/814#issuecomment-1368068800  

Thank you for your contribution.

---
