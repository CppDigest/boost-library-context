# #228 - Unterminated string literals should be rejected by the lexer [Open]

> Username: jefftrull  
> Created at: 2024-10-02 18:21:04 UTC  
> Updated at: 2024-10-03 00:21:26 UTC  
> Url: https://github.com/boostorg/wave/issues/228  

At the moment Wave accepts text like this:  
```  
"A  
B"  
```  
which a user might accidentally write, intending to create a multi-line string literal with an embedded newline. In fact these "classic" (pre C++11) string literals [disallow newlines](https://en.cppreference.com/w/cpp/language/string_literal).  
Unfortunately, Wave has been turning these into a series of tokens:  
```  
<UnknownToken>   (#34 ) at /tmp/blah.c (  1/ 1): >"<  
IDENTIFIER       (#380) at /tmp/blah.c (  1/ 2): >A<  
NEWLINE          (#394) at /tmp/blah.c (  1/ 3): >\n<  
IDENTIFIER       (#380) at /tmp/blah.c (  2/ 1): >B<  
<UnknownToken>   (#34 ) at /tmp/blah.c (  2/ 2): >"<  
```  
It can then go on to do work on these tokens, like directive evaluation, as described in bug #225.  
  
Wave should produce a lexer exception in this case instead.

---

## Comment 1

> Username: njnobles  
> Created at: 2024-10-02 19:17:32 UTC  
> Url: https://github.com/boostorg/wave/issues/228#issuecomment-2389502214  

Would it be possible to have a mode that still accepts multiline strings like your AB example?  
Our clients' code has pervasive usage of mutli-line strings unfortunately and it'll be difficult to find and update all instances while ensuring no unexpected changes in behavior.

---

## Comment 2

> Username: jefftrull  
> Created at: 2024-10-02 20:43:45 UTC  
> Url: https://github.com/boostorg/wave/issues/228#issuecomment-2389663413  

Maybe? But think a bit about what this means... we would have a mode whose behavior was basically undefined, as we will be unwilling to fix any issues involving the multiline strings. Your bug #225, for example. We definitely would not sign up to maintain the current behavior in any particular way beyond not throwing an exception when the mode was turned on.  
  
I think a script to convert the multiline strings to raw string literals might be doable. You could even write something using Wave!

---

## Comment 3

> Username: jefftrull  
> Created at: 2024-10-02 20:47:17 UTC  
> Url: https://github.com/boostorg/wave/issues/228#issuecomment-2389668771  

You might not even need raw string literals given string concatenation.  Just turn:  
  
```  
"a  
#ifdef B  
b"  
```  
into  
```  
"a\n"  
#ifdef B   
"b"  
```

---

## Comment 4

> Username: njnobles  
> Created at: 2024-10-02 22:24:02 UTC  
> Url: https://github.com/boostorg/wave/issues/228#issuecomment-2389791165  

Unfortunately, our custom DSLs don't support string concatenation (nor C++-style raw string literals) at the moment. I can't imagine it would be too difficult to add, but it certainly complicates my issue.   
I understand not providing any official support for it, but having the option would at least give my team some flexibility to update our DSLs and client code incrementally. Otherwise, we'll just have to pin ourselves to Boost 1.86.0 until we can sort it out.

---

## Comment 5

> Username: jefftrull  
> Created at: 2024-10-02 22:38:48 UTC  
> Url: https://github.com/boostorg/wave/issues/228#issuecomment-2389805475  

I wouldn't reject the idea of maintaining a custom fork of Wave, actually. You wouldn't have to pin yourself to 1.86 overall, thanks to [the Modular Boost project](https://www.boost.io/doc/user-guide/faq.html#_modular_boost). And you could fix #225 your way, if you wanted to.  
  
@hkaiser any thoughts about a super-secret disabling switch for the exception?

---

## Comment 6

> Username: hkaiser  
> Created at: 2024-10-03 00:21:26 UTC  
> Url: https://github.com/boostorg/wave/issues/228#issuecomment-2390237945  

> I wouldn't reject the idea of maintaining a custom fork of Wave, actually. You wouldn't have to pin yourself to 1.86 overall, thanks to [the Modular Boost project](https://www.boost.io/doc/user-guide/faq.html#_modular_boost). And you could fix #225 your way, if you wanted to.  
>   
> @hkaiser any thoughts about a super-secret disabling switch for the exception?  
  
```  
#ifndef BOOST_WAVE_DISABLE_SUPER_SECRET_EXCEPTION  
    throw ...  
#endif  
```  
?
