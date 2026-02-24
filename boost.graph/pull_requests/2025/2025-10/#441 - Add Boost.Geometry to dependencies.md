# #441 Add Boost.Geometry to dependencies [Closed]

> Username: jeremy-murphy  
> Created at: 2025-10-13 22:40:37 UTC  
> Updated at: 2025-12-31 07:13:57 UTC  
> Closed at: 2025-12-31 07:13:56 UTC  
> Url: https://github.com/boostorg/graph/pull/441  



---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2025-10-13 22:51:39 UTC  
> Updated_at: 2025-10-13 22:52:36 UTC  
> Url: https://github.com/boostorg/graph/pull/441#issuecomment-3399318083  

@grafikrobot , did I add it correctly?  
  
Or is BGL the first library to depend on Boost.Geometry and test this part of the build system?

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2025-10-14 20:38:31 UTC  
> Url: https://github.com/boostorg/graph/pull/441#issuecomment-3403540209  

> @grafikrobot , did I add it correctly?  
  
Yes.  
  
> Or is BGL the first library to depend on Boost.Geometry and test this part of the build system?  
  
Don't know.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2025-10-15 00:58:19 UTC  
> Url: https://github.com/boostorg/graph/pull/441#issuecomment-3404139003  

```  
  The link interface of target "boost_geometry" contains:  
  
    Boost::crc  
  
  but the target was not found.  Possible reasons include:  
  
    * There is a typo in the target name.  
    * A find_package call is missing for an IMPORTED target.  
    * An ALIAS target is missing.  
  
```  
OK, so this is a bug in Boost.Geometry?

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2025-10-22 09:57:40 UTC  
> Url: https://github.com/boostorg/graph/pull/441#issuecomment-3431478042  

@pdimov do you maintain Boost.CRC? Do you know if this problem is with Geometry or CRC?

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-10-22 12:38:38 UTC  
> Updated_at: 2025-10-22 12:39:08 UTC  
> Url: https://github.com/boostorg/graph/pull/441#issuecomment-3432157618  

It's not a problem in CRC. It's caused by Geometry listing CRC as a dependency in its CMakeLists.txt here:  
  
https://github.com/boostorg/geometry/blob/8aaf504091cfb46541b0a014cb0778c4d7754b51/CMakeLists.txt#L27  
  
but it doesn't actually depend on CRC.

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2025-10-23 01:36:08 UTC  
> Url: https://github.com/boostorg/graph/pull/441#issuecomment-3434770349  

@pdimov thanks for answering. But... why does that cause this error, just because it doesn't actually use it?

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-10-23 01:41:33 UTC  
> Url: https://github.com/boostorg/graph/pull/441#issuecomment-3434777653  

Because only the actual dependencies are installed by depinst, so libs/crc is empty and the target Boost::crc doesn't exist.

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2025-10-23 02:49:46 UTC  
> Url: https://github.com/boostorg/graph/pull/441#issuecomment-3434876420  

Thanks for the explanation. PR to fix Boost.Geometry: https://github.com/boostorg/geometry/pull/1437

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2025-12-31 07:13:56 UTC  
> Url: https://github.com/boostorg/graph/pull/441#issuecomment-3701601495  

Made redundant by new implementation that doesn't use Boost.Geometry.

---
