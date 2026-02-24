# #603 - remove_spikes does not remove spike [Open]

> Username: vschoech  
> Created at: 2019-06-21 09:51:10 UTC  
> Updated at: 2025-03-27 10:00:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/603  

My "tc::geo::polygon" type is actually a multi-polygon, using a polygon type that is based on **int**, oriented **counter-clockwise** and **open** (not closed). I am using **boost 1.70.0**. Please consider the following example:   
  
```C++  
tc::geo::polygon<int> polygon_;  
boost::geometry::read_wkt("MULTIPOLYGON(((4287 2160,5984 2160,4287 2160)))", polygon_);  
boost::geometry::remove_spikes(polygon_);  
boost::geometry::is_empty(polygon_); // **** returns false! ****  
```  
  
As far as I can tell from our discussions, the correct result would be the empty multi-polygon. Here is the summary of my understanding of this issue:   
http://lists.boost.org/geometry/2014/11/3237.php  
  
See also:  
https://svn.boost.org/trac10/ticket/9871

---

## Comment 1

> Username: vschoech  
> Created at: 2020-03-09 10:21:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/603#issuecomment-596444267  

As of boost 1.72.0, this problem is still present as reported!

---

## Comment 2

> Username: vschoech  
> Created at: 2020-06-17 10:17:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/603#issuecomment-645286562  

As of boost 1.73.0, this problem is still present as reported!

---

## Comment 3

> Username: vschoech  
> Created at: 2021-03-26 17:08:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/603#issuecomment-808378426  

Confirming that this issue is still present in boost 1.75.0. Looking forward to the next milestone!

---

## Comment 4

> Username: vschoech  
> Created at: 2022-07-08 11:00:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/603#issuecomment-1178853325  

Confirming that this issue is still present in boost 1.79.0. Looking forward to the next milestone!

---

## Comment 5

> Username: Vishnu-C  
> Created at: 2022-09-21 06:14:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/603#issuecomment-1253252597  

I too confirm that it is still present in boost 1.79.0.  
  
![image](https://user-images.githubusercontent.com/10018119/191427919-2139bc1d-dd0f-4d24-a304-486ceed8605c.png)

---

## Comment 6

> Username: vschoech  
> Created at: 2023-08-15 15:33:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/603#issuecomment-1679139300  

Any news regarding this issue? @barendgehrels

---

## Comment 7

> Username: vschoech  
> Created at: 2023-08-28 13:15:32 UTC  
> Updated at: 2023-08-28 13:41:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/603#issuecomment-1695682468  

:warning: Still reproducible in the same way in boost 1.83.0.

---

## Comment 8

> Username: vschoech  
> Created at: 2025-03-27 10:00:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/603#issuecomment-2757452491  

⚠️ Still reproducible in the same way in boost 1.87.0.
