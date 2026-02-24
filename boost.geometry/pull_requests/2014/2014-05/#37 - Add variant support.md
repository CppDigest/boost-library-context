# #37 [assign] Add variant support [Merged]

> Username: sdebionne  
> Created at: 2014-05-23 08:10:40 UTC  
> Updated at: 2014-05-26 08:27:49 UTC  
> Merged at: 2014-05-23 22:30:55 UTC  
> Closed at: 2014-05-23 22:30:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/37  

This PR adds variant support to the assign (conversion) algorithm. I'm not sure that the other assign (e.g. assign_values) algorithms need variant support.  
Note to reviewers : I could not implement the test the way it is done in test_length.hpp (that was suggested by Bruno in an other PR), that sounded not very practical.

---

## Comment 1

> Username: brunolalande  
> Created_at: 2014-05-23 10:32:05 UTC  
> Updated_at: 2014-05-23 14:34:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/37#discussion_r12993716  

I'm surprised that this works. How come it doesn't fail to compile when Geometry1 is of variant type?

---

## Comment 2

> Username: sdebionne  
> Created_at: 2014-05-23 12:01:41 UTC  
> Updated_at: 2014-05-23 14:34:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/37#discussion_r12995651  

The checker is specialized for variant and it's a nop.  
  
```  
template <BOOST_VARIANT_ENUM_PARAMS(typename T)>  
struct checker<boost::variant<BOOST_VARIANT_ENUM_PARAMS(T)> >  
{};  
  
template <BOOST_VARIANT_ENUM_PARAMS(typename T)>  
struct checker<boost::variant<BOOST_VARIANT_ENUM_PARAMS(T)> const>  
{};  
```

---

## Comment 3

> Username: brunolalande  
> Created_at: 2014-05-23 12:55:12 UTC  
> Updated_at: 2014-05-23 14:34:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/37#discussion_r12996920  

So, same as with the other one, can you move the checks to resolve_variant?  
This no-op thing is going to go, most probably. Still not sure why it's  
here.  
  
Thanks  
Bruno

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2014-05-24 09:03:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/37#discussion_r13025467  

Is there a specific reason that these checks are moved inside the resolve_variant?  
All these kind of checks should be done as early as possible. That is on purpose everywhere like this, I see it being moved in some places.  
But if not possible (e.g. there should be resolved something), it is of course OK to move it.  
  
Sorry I come quite late with this, I was not really available last days, and since then a lot happened :-)

---

## Comment 5

> Username: brunolalande  
> Created_at: 2014-05-24 14:59:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/37#discussion_r13026669  

Hi Barend,  
  
We had discussed this at some point when I was working on variants. Basically, concept checking can only be done once you have a concrete geometry. Apparently right now there's a specialization for variant that does nothing and thus allows the free function check to pass. I don't remember having written that so I need to check but it looks a bit hacky: check in free function, potentially doing nothing if it's a variant, and then check again in resolve_variant to have the concrete geometries properly checked if a variant was passed initially.  
  
It's better to have everything done at one place, and that's possible only once the variants (if any) are resolved. So the workflow is: resolve variant -> check concepts -> resolve default strategy -> dispatch.  
  
So this is litterally "as early as possible", at least if we don't want to hack around. I know it can make the error messages a bit less clear. Does that impact them heavily on MSVC?  
  
Regards  
Bruno

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2014-05-24 16:41:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/37#discussion_r13027019  

Hi Bruno,  
OK, yes. I now remember we mailed about this. You are right, we cannot check the concepts here (as long as the concept check is not variant aware ;-) ).  
So "as early as possible" is then as soon as variants are resolved. This probably also accounts for dimension checks etc. OK for me.  
  
Yes I wanted to check the influence on early/later checks for compiler messages, but did not do that yet.  
  
Regards, Barend

---
