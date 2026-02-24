# #312 Without boost::geometry:: compiler try to use distance defined in my … [Merged]

> Username: ifedor88  
> Created at: 2015-06-29 09:26:45 UTC  
> Updated at: 2015-06-29 12:26:03 UTC  
> Merged at: 2015-06-29 12:26:03 UTC  
> Closed at: 2015-06-29 12:26:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/312  

…custom point class.  
  
Microsoft Visual Studio 2008

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-06-29 11:05:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/312#issuecomment-116609768  

Would the `geometry` namespace be enough?  
  
```  
return geometry::distance(...);  
```  
  
I'm asking because we use `boost` namespace for components of other libraries so if we used this namespace with `distance()` it could be confusing.

---

## Comment 2

> Username: ifedor88  
> Created_at: 2015-06-29 11:17:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/312#issuecomment-116614595  

Yeah, you're right. The geometry namespace would be enough.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-06-29 12:25:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/312#issuecomment-116636136  

Thanks!

---
