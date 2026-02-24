# #82 New macros galore (mostly codegen related) [Closed]

> Username: psiha  
> Created at: 2015-11-16 23:08:45 UTC  
> Updated at: 2018-11-02 13:49:18 UTC  
> Closed at: 2018-11-02 13:49:18 UTC  
> Url: https://github.com/boostorg/config/pull/82  

New macros that mostly control codegen and/or provide optimiser hints. I don't expect this PR to be accepted as is, rather as a discussion starting point on what to do 'with all this'. I'll start a dedicated boost.devel thread where everyone can chime in...  
  
BOOST_ASSUME(_UNCHECKED)()  
BOOST_UNREACHABLE(_UNCHECKED)()  
BOOST_UNREACHABLE()  
BOOST_DEFAULT_CASE_UNREACHABLE(_UNCHECKED)()  
  
BOOST_NOVTABLE  
BOOST_OPTIMIZE_FOR_SPEED_BEGIN()  
BOOST_OPTIMIZE_FOR_SPEED_END()  
BOOST_OPTIMIZE_FOR_SIZE_BEGIN()  
BOOST_OPTIMIZE_FOR_SIZE_END()  
BOOST_FAST_MATH_(ON/OFF/ON_BEGIN/ON_END/OFF_BEGIN/OFF_END)()  
  
BOOST_RESTRICTED  
BOOST_RESTRICTED_PTR  
BOOST_RESTRICTED_REF  
BOOST_RESTRICTED_THIS  
BOOST_NO_STRICT_ALIASING  
BOOST_HAS_UNION_TYPE_PUNNING_TRICK  
BOOST_HAS_MAY_ALIAS  
BOOST_MAY_ALIAS  
  
BOOST_HAS_OVERRIDABLE_SYMBOL  
BOOST_OVERRIDABLE_SYMBOL  
BOOST_OVERRIDABLE_MEMBER_SYMBOL  
BOOST_OVERRIDABLE_FUNCTION  
  
BOOST_CC_\* (calling conventions: CDECL, FASTCALL, STDCALL, VECTORCALL, STACK, REG_ABI, REG)  
  
BOOST_OVERRIDE  
BOOST_FINAL  
BOOST_NOTHROW_LITE  
  
BOOST_ATTRIBUTES( ... ) function attributes macro, attributes prefixed with BOOST_:  
- EXCEPTIONLESS  
- DOES_NOT_RETURN  
- WARN_UNUSED_RESULT  
- RESTRICTED_FUNCTION_L1/L2/L3  
- RESTRICTED_FUNCTION_RETURN  
- HOT  
- COLD  
- MINSIZE

---

## Comment 1

> Username: psiha  
> Created_at: 2016-09-03 21:13:21 UTC  
> Url: https://github.com/boostorg/config/pull/82#issuecomment-244570065  

FWIW: standalone version @ https://github.com/psiha/config_ex until I finally catch the time to apply the input from http://lists.boost.org/Archives/boost/2015/11/226556.php to actually get this included...

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-11-01 22:19:37 UTC  
> Url: https://github.com/boostorg/config/pull/82#issuecomment-435206473  

Reminder - this has been sitting here for a long time untouched.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-11-02 13:49:18 UTC  
> Url: https://github.com/boostorg/config/pull/82#issuecomment-435385857  

As I recall this was raised on the ML and didn't find much support, IMO it's too much in one go as well, unless there are calls from library authors for support for this I'm not inclined to merge.  
  
Closing for now.

---
