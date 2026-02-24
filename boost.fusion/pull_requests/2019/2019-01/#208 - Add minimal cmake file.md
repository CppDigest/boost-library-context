# #208 [CMake] Add minimal cmake file [Merged]

> Username: Mike-Devel  
> Created at: 2019-01-23 18:10:20 UTC  
> Updated at: 2019-03-08 07:58:32 UTC  
> Merged at: 2019-03-07 23:55:47 UTC  
> Closed at: 2019-03-07 23:55:48 UTC  
> Url: https://github.com/boostorg/fusion/pull/208  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling (Providedthat also all dependencies where added to the project before):  
  
    add_subdirectory( <path-to-boost_fusion> )   
    target_link_libraries( <my_lib> PUBLIC Boost::fusion )  
  
  
More information:  
  
- https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M  
- https://groups.google.com/d/msg/boost-developers-archive/4HU-RzReL7U/FS1X6OFrEQAJ  
- https://groups.google.com/forum/#!topic/boost-developers-archive/9ZdrVbAn1aU  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.  
  
Note: The cmake file depends on https://github.com/boostorg/mpl/pull/40, but I expect this to get merged soon, so I think there is no harm in merging this PR already.

---

## Comment 1

> Username: djowel  
> Created_at: 2019-01-23 23:24:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/208#issuecomment-457005880  

Looks good!

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2019-01-24 09:15:09 UTC  
> Updated_at: 2019-01-24 09:15:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/208#issuecomment-457123112  

Seems I was premature with my expectation that https://github.com/boostorg/mpl/pull/40 will get merged soon :( -- sorry for the noise.

---

## Review 3 [Commented]

> Username: Flast  
> Created_at: 2019-01-27 23:23:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fusion/pull/208#pullrequestreview-196850121  

📁 CMakeLists.txt

```diff
  23 |+ 		Boost::type_traits
  24 |+ 		Boost::typeof
  25 |+ 		Boost::utility
```

> Username: Flast  
> Created_at: 2019-01-27 23:23:56 UTC  
> Updated_at: 2019-01-28 12:02:54 UTC  
> Url: https://github.com/boostorg/fusion/pull/208#discussion_r251259999  

Should we update above lists when added dependency?

> Username: Mike-Devel  
> Created_at: 2019-01-28 11:56:17 UTC  
> Updated_at: 2019-01-28 12:02:54 UTC  
> Url: https://github.com/boostorg/fusion/pull/208#discussion_r251385061  

Yes, absolutely.


---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2019-03-04 21:50:31 UTC  
> Url: https://github.com/boostorg/fusion/pull/208#issuecomment-469435714  

Sorry for the long delay. All dependencies have a cmake file now and this PR can be merged.

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2019-03-08 07:58:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/208#issuecomment-470839745  

Thanks!

---
