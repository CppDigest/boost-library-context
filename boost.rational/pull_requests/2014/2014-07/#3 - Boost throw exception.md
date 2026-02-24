# #3 Boost throw exception [Merged]

> Username: jzmaddock  
> Created at: 2014-07-28 10:55:24 UTC  
> Updated at: 2015-04-02 02:25:42 UTC  
> Merged at: 2015-04-02 02:25:41 UTC  
> Closed at: 2015-04-02 02:25:41 UTC  
> Url: https://github.com/boostorg/rational/pull/3  

This patch changes the code to use BOOST_THROW_EXCEPTION throughout - this leads to a number of advantages including enhanced Boost.Exception support and very nearly support for building with exceptions disabled: currently everything except the iostreams operators work without exceptions.  
  
This patch also includes everything from https://github.com/boostorg/rational/pull/2, since it requires those enhanced tests.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-03-30 15:46:02 UTC  
> Url: https://github.com/boostorg/rational/pull/3#issuecomment-87728931  

LGTM

---

## Comment 2

> Username: ahmedcharles  
> Created_at: 2015-03-31 02:31:37 UTC  
> Url: https://github.com/boostorg/rational/pull/3#issuecomment-87904246  

I'd prefer this be a single commit that doesn't have the constexpr change that are from #2.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-03-31 08:51:44 UTC  
> Updated_at: 2015-03-31 11:17:26 UTC  
> Url: https://github.com/boostorg/rational/pull/3#discussion_r27462013  

You probably need to keep the space between `if` and `(`.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2015-03-31 08:56:15 UTC  
> Url: https://github.com/boostorg/rational/pull/3#issuecomment-88001204  

> I'd prefer this be a single commit that doesn't have the constexpr   
> change that are from #2 https://github.com/boostorg/rational/pull/2.  
  
Strictly speaking you're right.  However, if both PR's are accepted,   
then it makes no difference to the final commit history?

---

## Comment 5

> Username: ahmedcharles  
> Created_at: 2015-03-31 14:09:14 UTC  
> Url: https://github.com/boostorg/rational/pull/3#issuecomment-88101565  

You end up getting the simplest PR reviews and best history if the changes are contained in a single commit and they are rebased before being merged into the final branch. That's mostly my point, I suppose.

---
