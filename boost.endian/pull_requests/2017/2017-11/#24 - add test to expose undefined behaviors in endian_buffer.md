# #24 add test to expose undefined behaviors in endian_buffer [Merged]

> Username: arvidn  
> Created at: 2017-11-06 12:28:52 UTC  
> Updated at: 2017-12-22 14:35:38 UTC  
> Merged at: 2017-12-22 14:35:38 UTC  
> Closed at: 2017-12-22 14:35:38 UTC  
> Url: https://github.com/boostorg/endian/pull/24  

when instantiated with signed integers, ``endian_buffer`` performs right- and left shifts directly on those signed integers. For negative values, this invokes undefined- and implementation defined behavior.  
  
In my mind, the best solution is to avoid all bitwise-operators on signed integer types (and I count the shift operators as bitwise because of their restrictions).  
  
I propose all *values* are cast to its storage type, or an unsigned counterpart of themselves. All bit shifting and masking should be performed on the unsigned types.  
  
Here's an example output from running the tests added in this PR:  
  
``bjam cxxflags=-fsanitize=undefined linkflags=-fsanitize=undefined``  
  
```buffers.hpp:225:75: runtime error: left shift of negative value -128  
buffers.hpp:225:75: runtime error: left shift of negative value -32768  
buffers.hpp:225:75: runtime error: left shift of negative value -8388608  
buffers.hpp:225:75: runtime error: left shift of negative value -128  
```  
  
This issue is somewhat related to https://github.com/boostorg/endian/issues/19 in that a generic solution would be to explicitly define an interface between the value type and its corresponding storage type.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2017-12-20 21:33:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/endian/pull/24#pullrequestreview-84904209  

📁 test/buffer_test.cpp

```diff
 148 |+   void test_boundary_values()
 149 |+   {
 150 |+     endian_buffer<order::big, Int, sizeof(Int) * 8, align::no> bmax(
```

> Username: pdimov  
> Created_at: 2017-12-20 21:33:26 UTC  
> Updated_at: 2017-12-21 23:00:26 UTC  
> Url: https://github.com/boostorg/endian/pull/24#discussion_r158141524  

CHAR_BIT instead of 8 perhaps?


---

## Comment 2

> Username: pdimov  
> Created_at: 2017-12-21 13:48:41 UTC  
> Url: https://github.com/boostorg/endian/pull/24#issuecomment-353354927  

Would you mind replacing 8 with CHAR_BIT and rebasing against the current develop? I've added Travis and Appveyor so they'll run on the new commit.  
  
Also consider following the existing style and having a single function `test_boundary_conditions` called from `cpp_main`, which in turn contains these six tests.

---

## Comment 3

> Username: arvidn  
> Created_at: 2017-12-21 14:41:54 UTC  
> Url: https://github.com/boostorg/endian/pull/24#issuecomment-353367442  

Done. Keep in mind that UBsan doesn't trap on errors by default, it just prints an error to ``stderr``. So, when running it in regression tests, you probably want to pass ``cxxflags=-fsanitize-undefined-trap-on-error`` as well.

---

## Comment 4

> Username: arvidn  
> Created_at: 2017-12-21 14:46:16 UTC  
> Url: https://github.com/boostorg/endian/pull/24#issuecomment-353368506  

I build with: ``bjam -j4 darwin-stable cxxflags=-fsanitize=undefined linkflags=-fsanitize=undefined``  
  
(where ``darwin-stable`` is configured as a build of head of clang as of a few months ago).  
  
Running the test binary by hand (to see the error messages from UBsan):  
  
``$ bin/buffer_test.test/darwin-stable/debug/buffer_test``  
  
```  
/usr/local/include/boost/endian/buffers.hpp:225:75: runtime error: left shift of negative value -2  
/usr/local/include/boost/endian/buffers.hpp:225:75: runtime error: left shift of negative value -2                                                                                                                          6%    1:  1    
/usr/local/include/boost/endian/buffers.hpp:225:75: runtime error: left shift of negative value -259  
/usr/local/include/boost/endian/buffers.hpp:225:75: runtime error: left shift of negative value -66052  
/usr/local/include/boost/endian/buffers.hpp:225:75: runtime error: left shift of negative value -2  
/usr/local/include/boost/endian/buffers.hpp:225:75: runtime error: left shift of negative value -259  
/usr/local/include/boost/endian/buffers.hpp:225:75: runtime error: left shift of negative value -66052  
/usr/local/include/boost/endian/buffers.hpp:225:75: runtime error: left shift of negative value -16909061  
/usr/local/include/boost/endian/buffers.hpp:225:75: runtime error: left shift of negative value -4328719366  
/usr/local/include/boost/endian/buffers.hpp:225:75: runtime error: left shift of negative value -1108152157447  
/usr/local/include/boost/endian/buffers.hpp:225:75: runtime error: left shift of negative value -283686952306184  
```  
  
My impression is that ubsan is a bit more primitive than asan in that you only get trap-on-error *or* helpful error messages. Not both.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-12-21 15:00:37 UTC  
> Url: https://github.com/boostorg/endian/pull/24#issuecomment-353372149  

I was thinking that we just get the messages and b2 captures those. But https://github.com/google/sanitizers/wiki/SanitizerCommonFlags says that all sanitizers (a) support the `exitcode=1` option and (b) it's 1 by default; so UBsan _should_ in principle override the exit code, causing the test to fail.

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-12-21 15:07:13 UTC  
> Url: https://github.com/boostorg/endian/pull/24#issuecomment-353373785  

In https://gcc.gnu.org/onlinedocs/gcc/Instrumentation-Options.html there's mention of `-fno-sanitize-recover` and `halt_on_error=1` that might need to be used.  
  
Sanitizers don't seem overly documented.

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-12-21 15:08:35 UTC  
> Url: https://github.com/boostorg/endian/pull/24#issuecomment-353374125  

Ideally, we want all the messages and a nonzero exit code, but just the first message and a nonzero exit code would do as well.

---
