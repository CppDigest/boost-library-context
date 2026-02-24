# #17  Upgrade boost::parameter::aux::tagged_argument [Merged]

> Username: CromwellEnage  
> Created at: 2018-10-19 06:22:18 UTC  
> Updated at: 2018-10-24 12:48:10 UTC  
> Merged at: 2018-10-23 19:02:02 UTC  
> Closed at: 2018-10-23 19:02:02 UTC  
> Url: https://github.com/boostorg/parameter/pull/17  

Add support for passing functions as arguments to Boost.Parameter-enabled functions.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2018-10-19 12:00:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/parameter/pull/17#pullrequestreview-166491100  

📁 include/boost/parameter/aux_/tagged_argument.hpp

```diff
  49 |+     typedef typename ::boost::mpl::if_<
  50 |+         ::boost::is_function<arg_type>
  51 |+ #if defined(BOOST_NO_CXX11_HDR_FUNCTIONAL)
```

> Username: pdimov  
> Created_at: 2018-10-19 12:00:47 UTC  
> Updated_at: 2018-10-23 07:42:10 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#discussion_r226621806  

If you make msvc-11.0 use boost::function here, does the need to have workarounds when using it disappear?

> Username: CromwellEnage  
> Created_at: 2018-10-19 15:15:07 UTC  
> Updated_at: 2018-10-23 07:42:10 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#discussion_r226683692  

I just tried it on my development branch.  Unfortunately, I still need the workarounds in the test program, or it won't work for msvc-11.0.

> Username: CromwellEnage  
> Created_at: 2018-10-22 06:42:39 UTC  
> Updated_at: 2018-10-23 07:42:10 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#discussion_r226910006  

I'm still attempting to make the workarounds for msvc-11.0 unnecessary in user code.  I'm focusing my efforts on boost::parameter::keyword.  My goal is to implement an additional overloaded assignment operator that wraps the tag metafunction return type around boost::function wrapped around the argument, then use boost::enable_if et. al. with boost::is_function to avoid compiler errors mentioning ambiguous calls.  Here are my issues:  
  
1. If both overloads take in T const&, then compose.cpp compiles and links but fails to execute because the compiler never resolves the call with the function-accepting overload.  
2. If the function-accepting overload takes in T while the other overload takes in T const&, then I still encounter the ambiguous-call compiler error because I haven't figured out how to use boost::lazy_disable_if with boost::is_function for the other overload.  
3. If I convert the assignment operator overloads to overloads of a static helper function and use boost::declval with either decltype or BOOST_TYPEOF_TPL to compute the return type of the driver assignment operator, then compose.cpp and other test programs fail to compile because the static helper function cannot be found.  
  
If there are more promising approaches that I'm overlooking, please let me know.

> Username: CromwellEnage  
> Created_at: 2018-10-22 06:42:55 UTC  
> Updated_at: 2018-10-23 07:42:10 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#discussion_r226910044  

I'm still attempting to make the workarounds for msvc-11.0 unnecessary in user code.  I'm focusing my efforts on boost::parameter::keyword.  My goal is to implement an additional overloaded assignment operator that wraps the tag metafunction return type around boost::function wrapped around the argument, then use boost::enable_if et. al. with boost::is_function to avoid compiler errors mentioning ambiguous calls.  Here are my issues:  
  
1. If both overloads take in T const&, then compose.cpp compiles and links but fails to execute because the compiler never resolves the call with the function-accepting overload.  
2. If the function-accepting overload takes in T while the other overload takes in T const&, then I still encounter the ambiguous-call compiler error because I haven't figured out how to use boost::lazy_disable_if with boost::is_function for the other overload.  
3. If I convert the assignment operator overloads to overloads of a static helper function and use boost::declval with either decltype or BOOST_TYPEOF_TPL to compute the return type of the driver assignment operator, then compose.cpp and other test programs fail to compile because the static helper function cannot be found.  
  
If there are more promising approaches that I'm overlooking, please let me know.

> Username: CromwellEnage  
> Created_at: 2018-10-22 06:43:29 UTC  
> Updated_at: 2018-10-23 07:42:10 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#discussion_r226910132  

I'm still attempting to make the workarounds for msvc-11.0 unnecessary in user code.  I'm focusing my efforts on boost::parameter::keyword.  My goal is to implement an additional overloaded assignment operator that wraps the tag metafunction return type around boost::function wrapped around the argument, then use boost::enable_if et. al. with boost::is_function to avoid compiler errors mentioning ambiguous calls.  Here are my issues:  
  
1. If both overloads take in T const&, then compose.cpp compiles and links but fails to execute because the compiler never resolves the call with the function-accepting overload.  
2. If the function-accepting overload takes in T while the other overload takes in T const&, then I still encounter the ambiguous-call compiler error because I haven't figured out how to use boost::lazy_disable_if with boost::is_function for the other overload.  
3. If I convert the assignment operator overloads to overloads of a static helper function and use boost::declval with either decltype or BOOST_TYPEOF_TPL to compute the return type of the driver assignment operator, then compose.cpp and other test programs fail to compile because the static helper function cannot be found.  
  
If there are more promising approaches that I'm overlooking, please let me know.

> Username: CromwellEnage  
> Created_at: 2018-10-22 06:43:52 UTC  
> Updated_at: 2018-10-23 07:42:10 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#discussion_r226910192  

I'm still attempting to make the workarounds for msvc-11.0 unnecessary in user code.  I'm focusing my efforts on boost::parameter::keyword.  My goal is to implement an additional overloaded assignment operator that wraps the tag metafunction return type around boost::function wrapped around the argument, then use boost::enable_if et. al. with boost::is_function to avoid compiler errors mentioning ambiguous calls.  Here are my issues:  
  
1. If both overloads take in T const&, then compose.cpp compiles and links but fails to execute because the compiler never resolves the call with the function-accepting overload.  
2. If the function-accepting overload takes in T while the other overload takes in T const&, then I still encounter the ambiguous-call compiler error because I haven't figured out how to use boost::lazy_disable_if with boost::is_function for the other overload.  
3. If I convert the assignment operator overloads to overloads of a static helper function and use boost::declval with either decltype or BOOST_TYPEOF_TPL to compute the return type of the driver assignment operator, then compose.cpp and other test programs fail to compile because the static helper function cannot be found.  
  
If there are more promising approaches that I'm overlooking, please let me know.


---

## Review 2 [Commented]

> Username: CromwellEnage  
> Created_at: 2018-10-22 06:44:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/parameter/pull/17#pullrequestreview-166835432  

I'm still attempting to make the workarounds for msvc-11.0 unnecessary in user code.  I'm focusing my efforts on boost::parameter::keyword.  My goal is to implement an additional overloaded assignment operator that wraps the tag metafunction return type around boost::function wrapped around the argument, then use boost::enable_if et. al. with boost::is_function to avoid compiler errors mentioning ambiguous calls.  Here are my issues:  
  
1. If both overloads take in T const&, then compose.cpp compiles and links but fails to execute because the compiler never resolves the call with the function-accepting overload.  
2. If the function-accepting overload takes in T while the other overload takes in T const&, then I still encounter the ambiguous-call compiler error because I haven't figured out how to use boost::lazy_disable_if with boost::is_function for the other overload.  
3. If I convert the assignment operator overloads to overloads of a static helper function and use boost::declval with either decltype or BOOST_TYPEOF_TPL to compute the return type of the driver assignment operator, then compose.cpp and other test programs fail to compile because the static helper function cannot be found.  
  
If there are more promising approaches that I'm overlooking, please let me know.

---

## Comment 3

> Username: CromwellEnage  
> Created_at: 2018-10-22 06:45:13 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#issuecomment-431752565  

I'm still attempting to make the workarounds for msvc-11.0 unnecessary in user code.  I'm focusing my efforts on boost::parameter::keyword.  My goal is to implement an additional overloaded assignment operator that wraps the tag metafunction return type around boost::function wrapped around the argument, then use boost::enable_if et. al. with boost::is_function to avoid compiler errors mentioning ambiguous calls.  Here are my issues:  
  
1. If both overloads take in T const&, then compose.cpp compiles and links but fails to execute because the compiler never resolves the call with the function-accepting overload.  
2. If the function-accepting overload takes in T while the other overload takes in T const&, then I still encounter the ambiguous-call compiler error because I haven't figured out how to use boost::lazy_disable_if with boost::is_function for the other overload.  
3. If I convert the assignment operator overloads to overloads of a static helper function and use boost::declval with either decltype or BOOST_TYPEOF_TPL to compute the return type of the driver assignment operator, then compose.cpp and other test programs fail to compile because the static helper function cannot be found.  
  
If there are more promising approaches that I'm overlooking, please let me know.

---

## Comment 4

> Username: eldiener  
> Created_at: 2018-10-22 09:18:09 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#issuecomment-431782361  

Can you create a run-fail test which shows the msvc-11.0 failure condition in your changed code without your workaround in the test code ? That would be helpful in illustrating the problem in msvc-11.0 for those users who still use msvc-11.0.   
  
BTW I found, in some other library on which I am working which has nothing to do with parameter, that msvc-10.0 has problems with their std::function implementation in conjunction with std::ref and/or std::bind, whereas using boost::function with boost::ref and/or boost::bind works with that compiler. But if you have not run into any of that in your tests for your changes in parameter please do not worry about it. I thought I would mention it as just as a heads up.

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-10-22 09:19:57 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#issuecomment-431782796  

Can you create a run-fail test which shows the msvc-11.0 failure condition in your changed code without your workaround in the test code ? That would be helpful in illustrating the problem in msvc-11.0 for those users who still use msvc-11.0.   
  
BTW I found, in some other library on which I am working which has nothing to do with parameter, that msvc-10.0 has problems with their std::function implementation in conjunction with std::ref and/or std::bind, whereas using boost::function with boost::ref and/or boost::bind works with that compiler. But if you have not run into any of that in your tests for your changes in parameter please do not worry about it. I thought I would mention it as just as a heads up.

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-10-22 10:06:44 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#issuecomment-431793049  

It's not clear how much effort we need to invest into msvc-11.0 here. This is a new feature, if it can't be made to work there without a workaround, well, tough luck.

---

## Comment 7

> Username: CromwellEnage  
> Created_at: 2018-10-22 17:03:12 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#issuecomment-431898631  

I've been attempting to make the workarounds for msvc-11.0 unnecessary in  
user code, focusing my efforts on boost::parameter::keyword.  My goal was  
to implement an additional overload assignment operator that wraps the tag  
metafunction return type around boost::function wrapped around the  
argument, then use boost::enable_if et. al. with boost::is_function to  
avoid compiler errors mentioning ambiguous calls.  Here are my issues:  
  
1. If both overloads take in T const&, then compose.cpp compiles and links  
but fails to execute because the compiler never resolves the call with the  
function-accepting overload.  
2. If the function-accepting overload takes in T while the other overload  
takes in T const&, then I still encounter the ambiguous-call compiler error  
because I haven't figured out how to use boost::lazy_disable_if with  
boost::is_function for the other overload.  
3. If I convert the assignment operator overloads to overloads of a static  
helper function and use boost::declval with either decltype of  
BOOST_TYPEOF_TPL to compute the return type of the driver assignment  
operator, then compose.cpp and other test programs fail to compile because  
the static helper function cannot be found.  
  
It looks like GitHub is down at the moment, so I'll need to wait for that  
to clear up before I spin up a run-fail test.  In the meantime, if there  
are more promising approaches that I've overlooked, please let me know.

---

## Comment 8

> Username: eldiener  
> Created_at: 2018-10-22 17:34:09 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#issuecomment-431908501  

If this is a msvc-11.0 issue I agree with Peter and you should not bother further to find a workaround. Most vc++ programmers will be using V2013, VS2015, and VS2017 by now. As I suggested if you can create a run-fail test that shows the problem with msvc-11.0 that would be ideal, otherwise I do not think you should spend your time worrying about the bug of an older compiler like msvc-11.0 and trying to find a one-off solution for it.  If you want I can merge this as it is, and you can deal with this problem, if you like, in a further PR.

---

## Comment 9

> Username: CromwellEnage  
> Created_at: 2018-10-23 17:03:19 UTC  
> Url: https://github.com/boostorg/parameter/pull/17#issuecomment-432333474  

I've created the run-fail test.  The result isn't very descriptive, but it shows that a problem exists.

---
