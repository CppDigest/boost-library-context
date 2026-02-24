# #175 Use make_nvp from Core [Closed]

> Username: glenfe  
> Created at: 2019-10-01 12:02:01 UTC  
> Updated at: 2019-10-05 19:35:28 UTC  
> Closed at: 2019-10-05 19:32:13 UTC  
> Url: https://github.com/boostorg/serialization/pull/175  

After this is merged this, we will update Core so that `make_nvp` is also defined in namespace `boost::serialization`.

---

## Comment 1

> Username: robertramey  
> Created_at: 2019-10-01 15:38:07 UTC  
> Url: https://github.com/boostorg/serialization/pull/175#issuecomment-537095909  

Hmmmm - shouldn't   
using boost::make_nvp;  
using boost::nvp;  
  
Both be eliminated as both nvp and make_nvp are now going to be in the serialization namespace?

---

## Comment 2

> Username: glenfe  
> Created_at: 2019-10-01 15:53:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/175#issuecomment-537102637  

They _can_ be eliminated, yes. However, the reason we chose to put them in `boost::serialization` in Boost.Core is to avoid breaking users who forward declare them. But the documentation of Boost.Core will specify that they are available only from `boost::` (the fact that they are within `boost::serialization` is an implementation detail that we do not want to guarantee to new users).  
  
In Serialization, you have the choice to rely on that implementation detail, because we would never change it, but it would be nice if your view of them were also as Boost.Core facilities that you're bringing into Serialization.

---

## Comment 3

> Username: glenfe  
> Created_at: 2019-10-05 12:46:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/175#issuecomment-538646857  

@robertramey Note that until you merge this pull request I cannot move `make_nvp` into the `serialization` namespace, because all the tests would fail for all libraries using Serializaiton. Because then there would be two 'make_nvp' functions defined inside `boost::serialization`.

---

## Comment 4

> Username: glenfe  
> Created_at: 2019-10-05 19:34:34 UTC  
> Url: https://github.com/boostorg/serialization/pull/175#issuecomment-538681758  

I merged https://github.com/boostorg/core/commit/e94af0d41a31c994967e45f18e2503e51c44c537

---
