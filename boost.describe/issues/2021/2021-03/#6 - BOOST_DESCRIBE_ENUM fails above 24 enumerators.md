# #6 - BOOST_DESCRIBE_ENUM fails above 24 enumerators [Closed]

> Username: ddevienne  
> Created at: 2021-03-05 14:30:10 UTC  
> Updated at: 2021-03-15 15:06:07 UTC  
> Closed at: 2021-03-14 02:33:57 UTC  
> Url: https://github.com/boostorg/describe/issues/6  

Started integrating Boost.Describe in my code generator (as discussed on the Boost ML).  
  
Works on all my enums, except the largest with 27 enumerators. If I reduce the described enumerators to 24, then it's OK.  
(I didn't change the enum, my code would not compile anymore, just omitted a few enumerators in the DESCRIBE macro).  
  
Is there a preprocessor limit that needs to be increased?  
Aren't _native_ variadic macros _unlimited_ in number of arguments?  
What's one supposed to do in this case?  
  
No mention to that limit in https://pdimov.github.io/describe/doc/html/describe.html#implementation_limitations

---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-05 17:08:42 UTC  
> Url: https://github.com/boostorg/describe/issues/6#issuecomment-791555307  

There is unfortunately a limit of 24 in https://github.com/pdimov/describe/blob/eea288e04961374721020b5454707590815b2fcf/include/boost/describe/detail/pp_for_each.hpp, yes. You're right that it needs to be documented (after being increased appropriately).

---

## Comment 2

> Username: ddevienne  
> Created at: 2021-03-05 18:29:42 UTC  
> Url: https://github.com/boostorg/describe/issues/6#issuecomment-791601824  

Since I'm code-generating this, could there be lower-level macros I could use and combine,  
to not be limited to 24 in the single friendly macro? Something like a BEGIN_ENUM, ADD_ENUM x times, END_ENUM?

---

## Comment 3

> Username: pdimov  
> Created at: 2021-03-05 18:46:14 UTC  
> Url: https://github.com/boostorg/describe/issues/6#issuecomment-791610317  

That would be possible too, I suppose. BOOST_DESCRIBE_ENUM_BEGIN(E), BOOST_DESCRIBE_ENUM_IMPL(E, e) x N, BOOST_DESCRIBE_ENUM_END(E). I'm not going to make these part of the documented interface though. :-)  
  
I don't want to make changes while the review is ongoing, though, as judging by past experience reviewers often don't like this, even though the master branch would remain stable.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-03-14 02:33:57 UTC  
> Url: https://github.com/boostorg/describe/issues/6#issuecomment-798822095  

The limit is 52 now, and there are _BEGIN, _ENTRY, _END (undocumented) macros.

---

## Comment 5

> Username: ddevienne  
> Created at: 2021-03-15 15:06:07 UTC  
> Url: https://github.com/boostorg/describe/issues/6#issuecomment-799494169  

I'm updated to the latest, and all my enums are OK now. No new issues or warnings on VS 2019 or GCC 9.1. Thanks.
