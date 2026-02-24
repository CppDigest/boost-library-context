# #19 Check if f is valid before dereference [Closed]

> Username: ya1gaurav  
> Created at: 2015-10-20 08:59:25 UTC  
> Updated at: 2015-10-24 07:55:15 UTC  
> Closed at: 2015-10-24 07:55:15 UTC  
> Url: https://github.com/boostorg/regex/pull/19  

file = f at line no 228.  
file is checked against NULL at 232, but f is not checked. Adding null check.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-10-23 12:31:06 UTC  
> Url: https://github.com/boostorg/regex/pull/19#issuecomment-150559011  

The existing check is redundant actually - the only places where that constructor is called, the mapfile pointer comes from "this" which is always non-null.  Changed to an assert.

---

## Comment 2

> Username: ya1gaurav  
> Created_at: 2015-10-24 03:26:47 UTC  
> Url: https://github.com/boostorg/regex/pull/19#issuecomment-150748105  

Added BOOST_ASSERT instead of Null check,

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2015-10-24 07:55:15 UTC  
> Url: https://github.com/boostorg/regex/pull/19#issuecomment-150773467  

Apologies for not being clear, I've already made this change to develop: https://github.com/boostorg/regex/commit/e824ddb35b856860a3c723738e8044b9d5bef9b1  
  
Thanks, John.

---
