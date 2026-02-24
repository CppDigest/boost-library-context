# #1192 - Error in validity (report) (was: multipolygon buffer result either invalid geometry or valid geometry but wrong) [Open]

> Username: gastald88  
> Created at: 2023-08-28 15:44:04 UTC  
> Updated at: 2024-11-25 18:37:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1192  

I want to buffer of a small amount the multipolygon in the following picture (I apologize if it is quite complicated, I tried to replicate the issue in a simpler shape but I could not make it):  
  
![image](https://github.com/boostorg/geometry/assets/37936526/6cda47db-7011-4603-bc44-ca06b641e65f)  
  
When applying the buffer algorithm, I have encountered two problems:   
1. if the buffer amount is 0.05, we obtain an invalid geometry due to self-intersections in the external border;  
2. if the buffer amount is 0.050005, we obtain a valid geometry, but the result is wrong (see image below).  
![image](https://github.com/boostorg/geometry/assets/37936526/2730afe2-ee52-4124-9db2-39dcbbd0a3e1)  
  
Here below a godbolt link to replicate this problem:  
https://godbolt.org/z/vM9ex7MrW

---

## Comment 1

> Username: barendgehrels  
> Created at: 2023-08-29 17:29:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1192#issuecomment-1697861838  

Can you please use it without `#define BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE`  
This is not supposed to be used by library users  
(maybe it was suggested here once, but that was erroneous).  
Can you remove the define?

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-08-29 17:33:11 UTC  
> Updated at: 2023-08-29 17:35:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1192#issuecomment-1697867147  

Just did it myself.  
  
```  
Program returned: 0  
Using Boost 1.83.0  
is poly valid? true  
  
buffer amount: 0.05  
is buffered poly valid? false  
validity check message: Geometry has invalid self-intersections. A self-intersection point was found at (1292.86, 1471.84); method: i; operations: i/u; segment IDs {source, multi, ring, segment}: {0, 0, -1, 259}/{0, 0, -1, 262}  
  
buffer amount: 0.050005  
is buffered poly valid? true  
validity check message: Geometry is valid  
```  
So the report looks still the same.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2023-08-29 17:41:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1192#issuecomment-1697877028  

Adding area, report is now:  
```  
buffer amount: 0.05000000000000000278  
area 4015945.12599748093634843826  
is buffered poly valid? false  
validity check message: Geometry has invalid self-intersections. A self-intersection point was found at (1292.86, 1471.84); method: i; operations: i/u; segment IDs {source, multi, ring, segment}: {0, 0, -1, 259}/{0, 0, -1, 262}  
  
buffer amount: 0.05000500000000000084  
area 4015945.17474469868466258049  
is buffered poly valid? true  
validity check message: Geometry is valid  
```  
  
It seems like the buffer has the same shape for both distances (still reporting one as invalid)  
  
Added:   
` << std::fixed << std::setprecision(20)`  
and, per buffer  
`<< "area " << boost::geometry::area(buffered_poly) << std::endl`

---

## Comment 4

> Username: gastald88  
> Created at: 2023-09-01 10:35:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1192#issuecomment-1702538739  

> Can you please use it without `#define BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` This is not supposed to be used by library users (maybe it was suggested here once, but that was erroneous). Can you remove the define?  
  
Thank you for the reply.   
  
As you said, I defined that macro as suggested in another issue of mine (see issue #1138), since that actually solved the problem. Do you think I should anyway remove the definition of `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` and wait for future fixes of #1138 not concerning such macro?

---

## Comment 5

> Username: barendgehrels  
> Created at: 2023-09-14 09:18:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/1192#issuecomment-1719075494  

It is not yet fixed, unlike #1138   
Yes, please remove the define `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE`, it is deprecated  
The validity check might be a false positive in this case. Therefore I'm not closing this issue. But the buffer itself looks reasonable.

---

## Comment 6

> Username: gastald88  
> Created at: 2023-09-14 09:24:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1192#issuecomment-1719085283  

perfect, thank you very much for the update.
