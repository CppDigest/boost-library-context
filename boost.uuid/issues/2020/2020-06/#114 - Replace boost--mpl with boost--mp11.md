# #114 - Replace boost::mpl with boost::mp11 [Closed]

> Username: ingo-loehken  
> Created at: 2020-06-15 10:44:08 UTC  
> Updated at: 2022-02-09 20:05:41 UTC  
> Closed at: 2022-02-09 20:05:40 UTC  
> Url: https://github.com/boostorg/uuid/issues/114  

If possible please replace boost::mpl with boost::mp11.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-06-15 11:16:11 UTC  
> Url: https://github.com/boostorg/uuid/issues/114#issuecomment-644066414  

This would break compatibility with C++03.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-06-15 11:25:28 UTC  
> Url: https://github.com/boostorg/uuid/issues/114#issuecomment-644070467  

Actually, I don't see where Boost.MPL is used in Boost.UUID. Can you point where the dependency exists?

---

## Comment 3

> Username: ingo-loehken  
> Created at: 2020-06-15 12:28:40 UTC  
> Url: https://github.com/boostorg/uuid/issues/114#issuecomment-644102699  

Yes compatibility is an issue.  
  
Besides that, maybe the issue can be solved in a different way.  
  
Include-Trace  
boost\1_72_0\include\boost/uuid/random_generator.hpp  
boost\1_72_0\include\boost/tti/has_member_function.hpp  
boost\1_72_0\include\boost/function_types/property_tags.hpp  
boost\1_72_0\include\boost/mpl/bitxor.hpp

---

## Comment 4

> Username: jeking3  
> Created at: 2022-02-04 20:21:14 UTC  
> Url: https://github.com/boostorg/uuid/issues/114#issuecomment-1030321332  

At some point I don't mind saying you have to use an older version of boost to use C++03.  I don't know if that some point is today though.  However in this case it is tti which uses function_types which uses mpl.  So we would either have to remove tti (@Lastique I think you added it in 2018) or drop the issue.

---

## Comment 5

> Username: Lastique  
> Created at: 2022-02-04 22:25:39 UTC  
> Updated at: 2022-02-04 22:30:22 UTC  
> Url: https://github.com/boostorg/uuid/issues/114#issuecomment-1030399034  

@jeking3 Actually, the dependency on Boost.TTI was introduced in a8a1ec3d826b04d2c842e79ecd18cff6a991fd29 by you. We could probably try to reimplement `has_member_function` locally, though I don't think this is a pressing issue.  
  
I also don't think Boost.UUID would gain much from dropping C++03 support.  
  
So, in a nutshell, this issue is not in Boost.UUID per se and can be closed, IMHO. If getting rid of TTI dependency is desired, this can be registered as a separate issue (for dropping the dependency, not for dropping C++03).

---

## Comment 6

> Username: jeking3  
> Created at: 2022-02-09 20:05:40 UTC  
> Url: https://github.com/boostorg/uuid/issues/114#issuecomment-1034146303  

That's fair.  Thanks for the assessment.
