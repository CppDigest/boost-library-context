# #148 - get_pointer issues on VS2017 in make_ptr_instance.hpp [Closed]

> Username: garyfurnish  
> Created at: 2017-08-21 08:11:36 UTC  
> Updated at: 2017-08-21 20:48:52 UTC  
> Closed at: 2017-08-21 20:48:52 UTC  
> Url: https://github.com/boostorg/python/issues/148  

If you alter VS to use unique_ptr instead of auto ptr, in   
`get_derived_class_object(boost::python::detail::true_, U const volatile* x)`  
U is derived such that x is a regular pointer, which causes linker errors as get_pointer is not defined for regular pointers.  Trying to work around this with an ifdef leads to massive errors elsewhere (the errors may not be causal, its not clear).  In any event, somewhere in here type deduction/dispatch is not working correctly and it appears to have been masked by auto_ptr.  This may or may not be a VS specific issue.  This bug report is rather vague, but I was hoping someone might have an idea of what is going on since I'm banging my head against a wall trying to figure it out given I can't get tests to run locally.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-08-21 12:48:14 UTC  
> Url: https://github.com/boostorg/python/issues/148#issuecomment-323735909  

Thanks for reporting this. I have seen (seemingly) similar symptoms on Windows, and couldn't figure out what was going on. So thanks for helping to clarify. I have only casual exposure to Windows (and much less to MSVC), so my help will be of limited value. Sorry.

---

## Comment 2

> Username: RiccardoRossi  
> Created at: 2017-08-21 19:40:35 UTC  
> Url: https://github.com/boostorg/python/issues/148#issuecomment-323833391  

Hi,   
    I have no insight in the error, however I Don t think it is the same as in #116 as that error was corrected in msvc 2017, unless it got broken again in the latest update of msvc2017 which was released last week  
  
Having said this I have the impression that support is currently a bit brittle (and I am actually concerned about that) however I have absolutely no suggestions to make...

---

## Comment 3

> Username: garyfurnish  
> Created at: 2017-08-21 20:07:33 UTC  
> Url: https://github.com/boostorg/python/issues/148#issuecomment-323839603  

Hmm, wait, the auto test suite is not using 2017,  does that mean args should fail?  Have I been banging my head against a known bug here?

---

## Comment 4

> Username: garyfurnish  
> Created at: 2017-08-21 20:48:52 UTC  
> Url: https://github.com/boostorg/python/issues/148#issuecomment-323849050  

Ok, so I figured out that AppVeyor does not actually use 2017, see #150, so this is actually not a real bug, as it is always using the 2015 buggy compiler.
