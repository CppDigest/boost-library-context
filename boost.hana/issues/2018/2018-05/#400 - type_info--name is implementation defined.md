# #400 - type_info::name is implementation defined [Closed]

> Username: xiangfan-ms  
> Created at: 2018-05-29 20:48:52 UTC  
> Updated at: 2018-06-20 03:52:43 UTC  
> Closed at: 2018-06-20 03:52:43 UTC  
> Url: https://github.com/boostorg/hana/issues/400  

hana\test\experimental\printable\metafunction.cpp will verify the result of 'hana::experimental::print' on certain types.  
  
The result is based on typeid:  
  
    return print_detail::strip_type_junk(boost::core::demangle(typeid(T).name()));  
  
So it is implementation defined and the result from MSVC is different from GCC and Clang.  
  
The result from MSVC can be found here: https://github.com/xiangfan-ms/hana/blob/master/test/experimental/printable/metafunction.cpp

---

## Comment 1

> Username: ricejasonf  
> Created at: 2018-05-29 22:40:38 UTC  
> Updated at: 2018-05-29 22:41:00 UTC  
> Url: https://github.com/boostorg/hana/issues/400#issuecomment-392970892  

It appears that the only difference is that in MSVC the types' names have `struct` prepended to them.  
  
I could have it conditionally strip that out. I'm not opposed to removing this feature entirely either.

---

## Comment 2

> Username: ldionne  
> Created at: 2018-05-30 04:04:07 UTC  
> Url: https://github.com/boostorg/hana/issues/400#issuecomment-393023573  

Would it make sense to tweak the printing facility so that it removes `struct` when on MSVC? Otherwise, we can just tweak the tests to be different on MSVC and other compilers.  
  
I don't have a strong preference, but I would like to keep this functionality since it is quite useful for debugging.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2018-05-30 16:49:26 UTC  
> Updated at: 2018-05-30 16:51:29 UTC  
> Url: https://github.com/boostorg/hana/issues/400#issuecomment-393233452  

This regex fulfills the original intent and it will likely pass on both platforms for these specific test cases.  
`(?:struct )?([a-z_]+::)*([a-z_]*)_t<(?:struct )?([a-z:<>_]*)>` substitute with `$2<$3>`  
  
I don't really have a way to test it for the Microsoft compiler though.

---

## Comment 4

> Username: ricejasonf  
> Created at: 2018-05-30 17:34:28 UTC  
> Url: https://github.com/boostorg/hana/issues/400#issuecomment-393251139  

https://github.com/boostorg/hana/pull/404

---

## Comment 5

> Username: ldionne  
> Created at: 2018-05-30 17:36:51 UTC  
> Url: https://github.com/boostorg/hana/issues/400#issuecomment-393251863  

Another option for fixing this issue would be to simply use a regex when performing the tests. This way, we don't try to provide deterministic results as the output of `hana::print`, but we still allow the tests to pass.

---

## Comment 6

> Username: ricejasonf  
> Created at: 2018-05-30 17:50:37 UTC  
> Updated at: 2018-05-30 17:58:04 UTC  
> Url: https://github.com/boostorg/hana/issues/400#issuecomment-393256354  

That is not trying to provide deterministic results. Nested types would still have `struct` in it. It cleans up the `struct boost::hana::*_t` part of it which was the original intent. I also took care of the top level `struct` in the type just to make it pass the tests.  
  
EDIT: Oh.. is that bad because it is making it inconsistent? (Do we *want* the preceding struct on the nested type?)

---

## Comment 7

> Username: xiangfan-ms  
> Created at: 2018-05-30 20:02:35 UTC  
> Url: https://github.com/boostorg/hana/issues/400#issuecomment-393298810  

Hi Jason,  
  
I'm interested in understanding the reason behind this comment to learn any potential blocking issues for people to try MSVC,  
  
> I don't really have a way to test it for the Microsoft compiler though.  
  
Some reasons I can think of,  
  
- No Windows machine  
- Don't have license for Visual Studio Professional  
There is a community version which is free to use  
- Don't want to install the IDE  
There is a Nuget package for Visual C++ compiler (about 800MB)  
- MSVC is closed source  
  
BTW, the particular test mentioned in this issue can be built by the latest public release of MSVC (15.7 RTM). AFAIK, older releases like VS2017 (15.0 RTM) also works.  
  
Thanks!

---

## Comment 8

> Username: ricejasonf  
> Created at: 2018-05-30 21:00:05 UTC  
> Url: https://github.com/boostorg/hana/issues/400#issuecomment-393317653  

@xiangfan-ms   
  
Those are all valid reasons.  
  
Even with gcc/clang I do not typically compile in my desktop environment. I use containers to create isolated build environments that are easy to automate. I can build locally for free or use a cloud service.  
  
AFAICT with MSVC, I would need to work from Windows using the GUI and mouse and all that.

---

## Comment 9

> Username: xiangfan-ms  
> Created at: 2018-05-31 21:50:49 UTC  
> Url: https://github.com/boostorg/hana/issues/400#issuecomment-393692240  

Thanks @ricejasonf for the feedback. I will communicate it back to the corresponding team.

---

## Comment 10

> Username: rhalbersma  
> Created at: 2018-06-04 19:46:46 UTC  
> Url: https://github.com/boostorg/hana/issues/400#issuecomment-394475879  

@xiangfan-ms The nicest thing MSFT could do is add the latest compilers (like: every point release and preview) to godbolt.org (only has VS 2017 RTW, not any of the updates or previews) or wandbox.org (only gcc/clang). This would make it a lot easier to test small examples so that people would use it on StackOverflow. This would shorten your feedback cycle enormously.

---

## Comment 11

> Username: ldionne  
> Created at: 2018-06-20 03:52:43 UTC  
> Url: https://github.com/boostorg/hana/issues/400#issuecomment-398616195  

Fixed in https://github.com/boostorg/hana/pull/404
