# #168 [MSVC] fix error C2995 [Closed]

> Username: hia3  
> Created at: 2016-01-23 22:32:43 UTC  
> Updated at: 2017-12-02 22:17:52 UTC  
> Closed at: 2017-11-08 17:05:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/168  

MSVC-2015 can't handle decltype in function template argument list.  
C2995: function template has already been defined. Moving decltype out  
of the function fixes the problem.

---

## Comment 1

> Username: djowel  
> Created_at: 2016-01-24 00:32:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-174238524  

Can MSVC handle all the tests and examples with this fix?

---

## Comment 2

> Username: hia3  
> Created_at: 2016-01-24 05:00:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-174253843  

No. I have only 30 tests of x3 passing. 18 tests are still failing to compile. But rexpr_min works.  
  
Examples build successfully: x3_calc1.exe, x3_calc4.exe, x3_calc8.exe, x3_calc9.exe, x3_employee.exe, x3_num_list1.exe, x3_num_list4.exe, x3_rexpr.exe.  
  
I think there is like 1 or 2 more workarounds to do.  
  
For example:  
  
spirit/home/x3/core/call.hpp(57): error C2672: "operator __surrogate_func": overloaded function not found

---

## Comment 3

> Username: hia3  
> Created_at: 2016-01-25 05:56:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-174411906  

This fixes all MSVC2015-specific problems with the library itself. All tests are passing, all examples are compiling (except one test and one example, see below)  
  
There are still 2 issues:  
  
1) rexpr\rexpr_full\test\parse_rexpr_test.cpp(38): error C2664: unable to convert argument 4 from "const boost::filesystem::path::value_type *" to "std::string"  
  
This is probably boost::filesystem's defect (if it allows this same code to compile on another platforms).  
  
2) x3_char1-p3.test:  
boost/spirit/home/x3/char/any_char.hpp(57): error C2398: Element 1 and 2: narrowing conversion from "const wchar_t" to "char".  
  
I've tried clang - it gives warning about this line. This warning/error should be fixed, but I don't know how to convert wchar_t to char.

---

## Comment 4

> Username: djowel  
> Created_at: 2016-01-25 08:05:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#discussion_r50662541  

This is not idiomatic. Does it not work without the full qualification?

---

## Comment 5

> Username: djowel  
> Created_at: 2016-01-25 08:06:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#discussion_r50662688  

Why do you need full qualification?

---

## Comment 6

> Username: djowel  
> Created_at: 2016-01-25 08:06:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#discussion_r50662744  

Same here

---

## Comment 7

> Username: djowel  
> Created_at: 2016-01-25 08:07:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#discussion_r50662839  

Why change std::bind to boost::bind?

---

## Comment 8

> Username: djowel  
> Created_at: 2016-01-25 08:07:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#discussion_r50662881  

Why change to boost?

---

## Comment 9

> Username: hia3  
> Created_at: 2016-01-25 10:56:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#discussion_r50679413  

Because (without the full qualification) it results in internal compiler error. I just wanted to fix as much tests as I could. - I'm ok with rolling this change back as it is just a test.

---

## Comment 10

> Username: hia3  
> Created_at: 2016-01-25 11:02:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#discussion_r50679989  

With std::bind it does not compile, it static_assert-s "tuple index out of bounds". I've tried to remove this static_assert - compiles, works. Can rollback.

---

## Comment 11

> Username: hia3  
> Created_at: 2016-01-25 22:14:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-174692383  

And btw, this - https://github.com/boostorg/spirit/blob/547076384ac4d9d6c94298015119c759f02c4882/doc/x3/tutorial/actions.qbk#L63 should say we can omit the parameter.

---

## Comment 12

> Username: djowel  
> Created_at: 2016-01-25 22:28:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-174700723  

The basic problem with MSVC is the fragility of the compiler and support for c++14. Things like what you did can easily trigger internal compiler errors. To be honest, I'm reluctant to support the compiler, considering that further development can easily disrupt the code base due to this fragility. I am not sure I am convinced yet that MSVC is good enough.

---

## Comment 13

> Username: hia3  
> Created_at: 2016-01-25 22:35:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-174702615  

Who is sayng "support"? Just let it work if it works. Should I remove changes in x3 tests?

---

## Comment 14

> Username: djowel  
> Created_at: 2016-01-25 22:42:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-174705970  

either way, i'm not sure. let me think about it. x3 is supposed to be workaround free.

---

## Comment 15

> Username: hia3  
> Created_at: 2016-01-25 22:42:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-174706541  

Ok, I see. )

---

## Comment 16

> Username: djowel  
> Created_at: 2016-01-25 22:54:44 UTC  
> Updated_at: 2016-01-25 22:55:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-174712094  

give me some time to think about it, please. i DO value your patch.

---

## Comment 17

> Username: unjello  
> Created_at: 2016-04-11 12:27:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-208315424  

:+1: Hi there. Any chance these fixes will get to mainline? I hate staying on patched versions :)

---

## Comment 18

> Username: djowel  
> Created_at: 2016-04-11 21:25:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-208569305  

I'm very sorry, but I'll have to say no on this one :-(   
  
When MSVC supports C++14, then X3 will support MSVC meanwhile ... please use V2 (Qi). There will be more c++14-isms coming that will break more of the MSVC code. I don't want MSVC to tie us down to what idioms we will use. Thank you for your concern about Spirit running on MSVC. I think your energy will be best directed at the Microsoft development team in helping them produce a compliant compiler.

---

## Comment 19

> Username: unjello  
> Created_at: 2016-05-23 20:08:09 UTC  
> Updated_at: 2016-05-23 20:08:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-221080967  

Well... I would like to be able to persuade you, but the truth is I totally get your point. Just checked the second preview of latest MSVC, v15.0.25302.0 PREVIEW - and they still didn't catch up :/

---

## Comment 20

> Username: rubenvb  
> Created_at: 2016-07-11 08:02:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-231666870  

Perhaps it's more valuable to support MSVC's Clang frontend instead? That would mean a decent compiler and a different standard library.

---

## Comment 21

> Username: CaseyCarter  
> Created_at: 2016-07-28 00:41:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-235765108  

> Perhaps it's more valuable to support MSVC's Clang frontend instead? That would mean a decent compiler and a different standard library.  
  
Clang/C2 uses Microsoft's STL.

---

## Comment 22

> Username: mxc-commons  
> Created_at: 2016-07-29 01:53:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-236074856  

Some german Dax enterprises we are working for decided strategically for Microsoft. So I'd love to see MSVC support.

---

## Comment 23

> Username: timangus  
> Created_at: 2016-10-27 15:59:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-256688660  

Would you consider committing the BOOST_SPIRIT_DEFINE fix only (c51a013)? This at least makes it compile and be usable on VS2015, even if some of the tests fail. I was just about to submit a basically identical patch that I came up with independently. I submitted a bug report for the compiler too, FWIW: https://connect.microsoft.com/VisualStudio/feedback/details/3109357/cannot-overload-templated-functions-with-decltype-parameters

---

## Comment 24

> Username: unjello  
> Created_at: 2017-02-10 09:03:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-278892097  

I'm more than happy to report, that `VS/15.0.0-RC.4+26206.0`'s platform toolset handles (at least) this case :) 👍

---

## Comment 25

> Username: djowel  
> Created_at: 2017-02-10 09:12:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-278893909  

Awesome!

---

## Comment 26

> Username: Kojoley  
> Created_at: 2017-11-08 17:05:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-342884767  

I am closing the PR because it had grown with merge conflicts and was relevant to a beta compiler (according to @unjello the problem solved in RC).

---

## Comment 27

> Username: Kojoley  
> Created_at: 2017-12-02 22:17:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/168#issuecomment-348724254  

Replaced by #308

---
