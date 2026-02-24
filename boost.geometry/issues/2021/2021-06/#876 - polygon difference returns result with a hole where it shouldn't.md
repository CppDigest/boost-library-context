# #876 - polygon difference returns result with a hole where it shouldn't [Closed]

> Username: jessesna  
> Created at: 2021-06-30 15:51:34 UTC  
> Updated at: 2021-07-28 11:51:29 UTC  
> Closed at: 2021-07-28 11:51:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/876  

I'm trying to subtract the white from the red polygon. This should result in a polygon almost identical with the red one and without any holes. Instead the result is the original red polygon as outer ring and the white polygon as inner ring.  
  
Adding and removing seemingly unrelated points, f.e. points 7, 8 and 9 from the red polygon, changes the behavior and makes it work correctly.  
  
Adding more precision supposedly could solve this example but i suspect it to be a problem inherent to the algorithm.  
  
![image](https://user-images.githubusercontent.com/78883245/123989374-21950400-d9c9-11eb-962a-b83d17a3c381.png)  
  
Here's [a godbolt showing the behavior](https://godbolt.org/z/zx9GKsfYM).  
  
After rotating the points of poly2 by one to the right, the problem disappears as shown in [this godbolt](https://godbolt.org/z/Y7Ge6Ksnf).  
`covered_by` seems to match with this behavior.  
  
[There's a entry on SO for this issue](https://stackoverflow.com/questions/67904576/boost-geometry-polygon-difference-returns-self-intersecting-result-or-is-wrongl).  
  
I think it's related to issue #875 .

---

## Comment 1

> Username: kleunen  
> Created at: 2021-07-03 18:04:41 UTC  
> Updated at: 2021-07-03 18:06:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/876#issuecomment-873446677  

Also in this case, if you define:   
````  
#define BOOST_GEOMETRY_NO_ROBUSTNESS  
````  
  
The output is correct:   
https://godbolt.org/z/87YsnT8Y9  
  
If I understand correctly, this is default behaviour in newer version of boost geometry.   
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/core/config.hpp#L30-L34

---

## Comment 2

> Username: jessesna  
> Created at: 2021-07-05 08:01:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/876#issuecomment-873894196  

I found an example which results in a self intersecting polygon using   
  
`#define BOOST_GEOMETRY_NO_ROBUSTNESS`  
  
https://godbolt.org/z/6vxe6bETs  
  
but doesn't without the define:  
  
https://godbolt.org/z/4ezP86xKh

---

## Comment 3

> Username: kleunen  
> Created at: 2021-07-05 08:23:34 UTC  
> Updated at: 2021-07-05 10:12:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/876#issuecomment-873911729  

You are moving close to the precision the float/double precision in the calculations. It is not a problem in the algorithm, float/double/long double have a limit in the precision they can provide. The intersection is removed again if you move to long double:  
https://godbolt.org/z/5vq9z9PKe  
  
If you need arbitrary precision, cgal is an option for this: https://www.cgal.org/  
  
boost geometry was designed for practical applications having a good performance, given the limitations of float/double/long double. CGAL allows mathematical like ordering calculations (inside/outside), but the performance penalty can be huge.   
  
Also read: https://www.cgal.org/exact.html

---

## Comment 4

> Username: barendgehrels  
> Created at: 2021-07-28 11:51:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/876#issuecomment-888246713  

I can reproduce this issue up to 1.76  
But in the current branch (which will be released into 1.77), it is correct - the message is `result poly 1 is not self intersecting:` with and without the define.  
I will add the testcase and I close this issue.
