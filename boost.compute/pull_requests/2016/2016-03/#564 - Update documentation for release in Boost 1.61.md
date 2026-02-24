# #564 Update documentation for release in Boost 1.61 [Merged]

> Username: kylelutz  
> Created at: 2016-03-07 02:52:15 UTC  
> Updated at: 2016-03-09 04:12:01 UTC  
> Merged at: 2016-03-09 04:12:00 UTC  
> Closed at: 2016-03-09 04:12:00 UTC  
> Url: https://github.com/boostorg/compute/pull/564  



---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-03-07 09:04:30 UTC  
> Url: https://github.com/boostorg/compute/pull/564#issuecomment-193166656  

I'm not sure if the information about downloading using `git clone` and the installation part should be gone. Note about Boost.Compute being available in Boost starting with version 1.61 is of course crucial and should be at the beginning of "Getting Started", but Boost.Compute can work without any other Boost library and even if some libs are required any Boost >=1.48 is enough (for now). However, I would move "Installation" after "Downloading" or merge them into one section.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-03-08 04:33:03 UTC  
> Url: https://github.com/boostorg/compute/pull/564#issuecomment-193601131  

Yeah, we should still keep these instructions around somewhere. But I think now that Boost.Compute is distributed together with Boost that we should reference Boost's download and installation instructions.  
  
We currently have instructions on downloading and installing from source on the developer's guide: https://github.com/boostorg/compute/wiki/Developer's-Guide. We could also provide these somewhere else for users who can't get Boost 1.61 or want a more recent version of just Boost.Compute.  
  
These changes are mainly based on looking at the documentation for a few other Boost libraries (like Boost.Proto). Let me know if you see other examples in existing Boost libraries on how to structure this better.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-03-08 11:51:01 UTC  
> Url: https://github.com/boostorg/compute/pull/564#issuecomment-193749112  

I think only [Hana](http://www.boost.org/doc/libs/master/libs/hana/doc/html/index.html) has a reference to its git repository and instructions on how to build it using CMake in the documentation. Maybe the README is a good place to put those instructions on downloading and installing from source + note that Boost.Compute is official Boost library available in Boost starting with version 1.61?  
  
If you could, please add `* [funcref boost::compute::reduce_by_key() reduce_by_key()]` to the [reference.qbk](https://github.com/boostorg/compute/blob/master/doc/reference.qbk#L92).

---

## Comment 4

> Username: kylelutz  
> Created_at: 2016-03-09 03:20:20 UTC  
> Url: https://github.com/boostorg/compute/pull/564#issuecomment-194095488  

Yeah, I think the readme is a good place to put that information. I'll update it.  
  
I'll also add the link for `reduce_by_key()`, thanks for catching that!

---
