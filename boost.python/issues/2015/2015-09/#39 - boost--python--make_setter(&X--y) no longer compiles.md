# #39 - boost::python::make_setter(&X::y) no longer compiles [Closed]

> Username: jwakely  
> Created at: 2015-09-02 11:42:57 UTC  
> Updated at: 2015-12-10 21:48:39 UTC  
> Closed at: 2015-09-02 12:07:49 UTC  
> Url: https://github.com/boostorg/python/issues/39  

This trivial example, similar to the examples in the Boost.Python documentation, compiled with 1.58 but doesn't with 1.59 (using various versions of GCC and Clang):  
  
```  
#include <boost/python.hpp>  
  
struct X { int y; };  
  
int main()  
{  
  boost::python::make_setter(&X::y);  
}  
```  
  
The relevant error is:  
  
`/usr/local/boost-1.59.0/include/boost/python/data_members.hpp:303:15: note: candidate: boost::python::api::object boost::python::make_setter(D&) [with D = int X::*] <near match>`  
  
<pre>    inline object make_setter(D& x)  
                  ^</pre>  
  
`/usr/local/boost-1.59.0/include/boost/python/data_members.hpp:303:15: note:   conversion of argument 1 would be ill-formed:`<BR>  
`prog.cc:7:37: error: invalid initialization of non-const reference of type 'int X::*&' from an rvalue of type 'int X::*'`  
  
<pre>     boost::python::make_setter(&X::y);  
                                     ^</pre>

---

## Comment 1

> Username: jwakely  
> Created at: 2015-09-02 12:00:11 UTC  
> Updated at: 2015-09-02 12:01:20 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-137047535  

This was caused by 42e7d7bb which broke the preprocessor condition for `make_setter` by inverting the sense of the test. There was a later change in 590b7887 but it didn't restore the old behaviour.

---

## Comment 2

> Username: afh  
> Created at: 2015-09-15 01:21:59 UTC  
> Updated at: 2015-09-15 01:45:10 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140246915  

What intermediate solution can projects (e.g. [ledger](https://github.com/ledger/ledger)) use to fix [this](http://sprunge.us/OhWe) until the next release of boost python?

---

## Comment 3

> Username: jwakely  
> Created at: 2015-09-15 09:20:33 UTC  
> Updated at: 2015-09-15 09:23:39 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140334550  

You could define the "missing" overload in your own code:  
  
```  
#if BOOST_VERSION == 105900  
// Fix for https://github.com/boostorg/python/issues/39  
namespace boost { namespace python {  
template <class D>  
inline object make_setter(D const& x)  
{  
    return detail::make_setter(x, default_call_policies(), is_member_pointer<D>(), 0);  
}  
}}  
#endif  
```

---

## Comment 4

> Username: afh  
> Created at: 2015-09-15 12:00:21 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140367646  

Thank you for your reply. I already tried that to no avail, which I find quite odd.  
I tried adding the "missing" overload in different places in the code, but still see errors like:  
  
```  
../src/py_amount.cc:131:26: error: no matching function for call to 'make_setter'  
                         make_setter(&amount_t::is_initialized))  
                         ^~~~~~~~~~~  
/opt/homebrew/include/boost/python/data_members.hpp:303:15: note: candidate function [with D = bool *] not viable: no known conversion from 'bool *' to 'bool *&' for 1st argument  
inline object make_setter(D& x)  
              ^  
/opt/homebrew/include/boost/python/data_members.hpp:291:15: note: candidate function template not viable: requires 2 arguments, but 1 was provided  
inline object make_setter(D& x, Policies const& policies)  
```

---

## Comment 5

> Username: jwakely  
> Created at: 2015-09-15 12:02:08 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140367964  

Looks like you didn't declare the overload everywhere it's needed, since it's not listed as a candidate function.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2015-09-15 12:13:53 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140369925  

(As usual, it might help to put together a minimal test case and share that. Otherwise it's hard to help just by second-guessing.)

---

## Comment 7

> Username: afh  
> Created at: 2015-09-15 12:18:04 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140370831  

@stefanseefeld The boostorg/python#40 fix works just fine. Yet I would like to make the project aware that boost python 1.59 has an issue and address that with a workaround within the project.

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2015-09-15 12:19:44 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140371244  

Yes, I understand. But even if you ask for help on your own code, it's hard to do so without seeing the actual code. (And often enough putting together a test case might in itself reveal the problem, so it's a good strategy anyhow. :-))

---

## Comment 9

> Username: jwakely  
> Created at: 2015-09-15 12:25:06 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140372092  

Be aware that Fedora rawhide (which will become Fedora 24) has a patched version of Boost 1.59.0 which includes the fix, so putting a workaround in the project might conflict with that patched version of Boost.

---

## Comment 10

> Username: afh  
> Created at: 2015-09-15 12:26:21 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140372286  

I'm afraid I may have had an unclean environment, in which I tested. With a fresh workspace and merely the proposed changes by @jwakely it compiles just fine.  
  
@stefanseefeld Thanks for the advice. I'll remember it for next time.

---

## Comment 11

> Username: afh  
> Created at: 2015-09-15 12:28:24 UTC  
> Updated at: 2015-09-15 12:29:02 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140372622  

@jwakely Thanks for the heads up. How would such an issue be addressed best?  
- Within the project's configuration step (for ledger that would be CMake)?  
- Add the fix for the "majority" of users and have it fail for the others?  
- Not all?  
- Deliver a patch that users can apply manually?

---

## Comment 12

> Username: jwakely  
> Created at: 2015-09-15 12:33:01 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140373387  

> Within the project's configuration step (for ledger that would be CMake)?  
  
Yes, I would add an autoconf / cmake test and only add the missing overload if needed.

---

## Comment 13

> Username: stefanseefeld  
> Created at: 2015-09-15 12:38:32 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140374370  

(This is entirely off-topic, but anyhow: Given that "#if BOOST_VERSION == 105900" appears to be the condition under which you'll need this patch, there is really nothing to configure for. Just put the above patch from @jwakely into your source code wherever needed and move on.)

---

## Comment 14

> Username: afh  
> Created at: 2015-09-15 13:29:24 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140393775  

@jwakely does the patched boost 1.59 version have a difference `BOOST_VERSION` number?

---

## Comment 15

> Username: stefanseefeld  
> Created at: 2015-09-15 13:36:29 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140395416  

There is no "patched boost 1.59 version". The patch is on master, and will be part of boost 1.60.

---

## Comment 16

> Username: afh  
> Created at: 2015-09-15 13:37:50 UTC  
> Updated at: 2015-09-15 13:38:03 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140395705  

@stefanseefeld I'm quoting @jwakely: _"Be aware that Fedora rawhide (which will become Fedora 24) has a patched version of Boost 1.59.0"_

---

## Comment 17

> Username: stefanseefeld  
> Created at: 2015-09-15 13:41:08 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140396449  

Oh, I stand corrected. Hmm, I don't like that idea. @jwakely, I think rather than having Fedora roll its own Boost.Python release, I think it would be better to try to get a new Boost.Python (bugfix) release out. (As you know, I have been trying to get Boost.Python to the point where I can do releases independently from the rest of Boost, anyhow.)  
In any case, the version string to check for is "105900". Anything else should not need the patch.

---

## Comment 18

> Username: jwakely  
> Created at: 2015-09-15 13:43:32 UTC  
> Updated at: 2015-09-15 13:43:53 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140397531  

Fedora rawhide has a patched Boost 1.59.0 with the same `BOOST_VERSION` of 105900 (otherwise I would not have bothered pointing it out).  
It's necessary to patch Boost for Fedora because Boost usually ships with several regressions in every release, and it's not an option to wait several months for another release, with another set of new regressions. I'm sure other distros carry patches too.

---

## Comment 19

> Username: stefanseefeld  
> Created at: 2015-09-15 13:46:11 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140399107  

@jwakely , that's very unfortunate, in particular as it doesn't allow users to discriminate based on BOOST_VERSION. There should be some way to check for the version using some macro.  
(I understand and agree with what you are saying, which is precisely why I want to decouple Boost.Python as much as possible. All I'm saying is that this should be coordinated a bit. Imagine developers who want to write portable code. What a nightmare !)

---

## Comment 20

> Username: afh  
> Created at: 2015-09-15 13:52:23 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140400634  

@afh raises :hand:. I want to write portable code and think this is (kind-of) a nightmare :smile:  
  
I think writing a CMake module that tests the issue by trying to compile the example given initially by @jwakely is a good idea.

---

## Comment 21

> Username: stefanseefeld  
> Created at: 2015-09-15 13:53:19 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140400841  

Yes, but it's only needed precisely because of that nightmare we are in. :-(

---

## Comment 22

> Username: jwakely  
> Created at: 2015-09-15 13:54:33 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140401120  

If Boost did 1.Y.1 releases more often (including critical fixes like this one and https://github.com/boostorg/log/commit/7da193fde1a9c1bc925ee980339f4df2e1a66fa7) then distros could use that, as it is we have no choice but to ship 1.Y.0 with all the breaking changes that entails, and then patch things to get the distro to build.

---

## Comment 23

> Username: stefanseefeld  
> Created at: 2015-09-15 13:57:28 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140401869  

@jwakely, yes, I agree. It's a systemic issue caused by the separation between project maintenance and distro maintenance. (How often have I filed bugs with RH's bugzilla only to be told that this is an "upstream" issue that can't be fixed by RH. As an end-user I don't want to have to care for "upstream"...) Anyhow, sorry for the ranting. I can fully understand and appreciate all the sides of this, as I have been part of this game for a long time... :-)

---

## Comment 24

> Username: jwakely  
> Created at: 2015-09-15 13:59:48 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140402466  

Understood.  For the next Boost release I plan to try rebuilding everything in Fedora with the release candidates, and try to report regressions _before_ the release, but that is a lot of work and didn't happen this time.

---

## Comment 25

> Username: afh  
> Created at: 2015-09-15 14:16:34 UTC  
> Updated at: 2015-09-15 14:17:14 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140406870  

Here's a little CMake snippet I'll use in order to check for this issue and  
include the "missing" overload when necessary:  
  
```  
cmake_push_check_state()  
  
set(CMAKE_REQUIRED_INCLUDES ${CMAKE_INCLUDE_PATH} ${Boost_INCLUDE_DIRS})  
set(CMAKE_REQUIRED_LIBRARIES ${Boost_LIBRARIES} ${PROFILE_LIBS})  
  
check_cxx_source_runs("  
#include <boost/python.hpp>  
  
struct X { int y; };  
  
int main()  
{  
  boost::python::make_setter(&X::y);  
}" BOOST_MAKE_SETTER_RUNS)  
  
if (BOOST_MAKE_SETTER_RUNS)  
  set(HAVE_BOOST_159_ISSUE_39 0)  
else()  
  set(HAVE_BOOST_159_ISSUE_39 1)  
endif()  
  
cmake_pop_check_state()  
```

---

## Comment 26

> Username: afh  
> Created at: 2015-09-15 15:02:39 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-140421696  

Thanks for the insightful conversation and the helpful comments about the issue at hand.  
  
I look forward to Boost 1.60.0 or the next Boost.Python release, whichever comes first :stuck_out_tongue_closed_eyes:

---

## Comment 27

> Username: kljohann  
> Created at: 2015-12-10 21:47:11 UTC  
> Updated at: 2015-12-10 21:48:39 UTC  
> Url: https://github.com/boostorg/python/issues/39#issuecomment-163758848  

@afh Note that you also need `${PYTHON_INCLUDE_DIRS}` and `${PYTHON_LIBRARIES}` (see `CMakeFiles/CMakeError.log` when compiling with a patched boost version). I pushed a fix in ledger/ledger@429765ee4.
