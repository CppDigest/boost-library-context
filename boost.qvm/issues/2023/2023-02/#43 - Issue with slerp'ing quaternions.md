# #43 - Issue with slerp'ing quaternions [Closed]

> Username: haraldF  
> Created at: 2023-02-24 15:20:59 UTC  
> Updated at: 2023-03-02 05:25:34 UTC  
> Closed at: 2023-03-02 05:25:34 UTC  
> Url: https://github.com/boostorg/qvm/issues/43  

Trying to slerp two quaternions:  
  
```c++  
#include <boost/qvm/quat.hpp>  
#include <boost/qvm/quat_access.hpp>  
#include <boost/qvm/quat_operations.hpp>  
  
#include <iostream>  
#include <iomanip>  
  
int main()  
{  
    const boost::qvm::quat<double> q1{0.0005261316933462083,0.01051085312843599,-0.0003732733078038091,0.9999445513722205};  
    const boost::qvm::quat<double> q2{0.006719932961540192,-0.008611214157320685,0.003628245065324309,-0.9999337604709};  
    const double t = 0.1891208547850313;  
  
    auto result = boost::qvm::slerp(q1, q2, t);  
  
    std::cerr << std::setprecision(16);  
    std::cerr << boost::qvm::S(result) << "," << boost::qvm::X(result) << "," << boost::qvm::Y(result) << "," << boost::qvm::Z(result) << std::endl;  
  
    return 0;  
}  
```  
  
Results in  
  
```  
0.4959416932143382,0.1385974162611532,0.2222754322128087,0.8279047199842609  
```  
  
which is quite wrong. I've verified with some other libraries and the result should something like  
  
```  
-0.0008442622481805555,0.01015164169514602,-0.00098886485767641,0.9999476253976719  
```  
  
I blindly hacked the code a bit and this works for me:  
  
```patch  
diff --git a/include/boost/qvm/quat_operations.hpp b/include/boost/qvm/quat_operations.hpp  
index 7cf87f8..8a5bca5 100644  
--- a/include/boost/qvm/quat_operations.hpp  
+++ b/include/boost/qvm/quat_operations.hpp  
@@ -721,7 +721,7 @@ slerp( A const & a, B const & b, C t )  
     TR const one = scalar_traits<TR>::value(1);  
     TR const threshold = one - one / scalar_traits<TR>::value(2000); //0.9995  
     TR dp = dot(a,b);  
-    if( dp > threshold )  
+    if( std::abs(dp) > threshold )  
         return normalized(a+(b-a)*t);  
     if( dp < -one )  
         dp = -one;  
```  
  
But I'm no mathematician so feel free to laugh at me if the above is naive ;)

---

## Comment 1

> Username: haraldF  
> Created at: 2023-02-25 11:28:25 UTC  
> Url: https://github.com/boostorg/qvm/issues/43#issuecomment-1445060394  

Looks like Eigen also takes the absolute of `dp`, see https://gitlab.com/libeigen/eigen/-/blob/master/Eigen/src/Geometry/Quaternion.h#L785

---

## Comment 2

> Username: haraldF  
> Created at: 2023-02-26 12:51:05 UTC  
> Url: https://github.com/boostorg/qvm/issues/43#issuecomment-1445353776  

For what it's worth, here's what the Bing AI thinks the function should look like. Also takes the `std::abs()`...  
  
  
```c++  
static quat slerp(const quat& q1, const quat& q2, double t) {  
        // Clamp t to [0, 1]  
        t = std::clamp(t, 0.0, 1.0);  
  
        // Normalize the input quaternions  
        quat p = normalize(q1);  
        quat r = normalize(q2);  
  
        // Compute the cosine of the angle between them  
        double cos_theta = dot(p, r);  
  
        // If they are too close or too far apart,  
        // use linear interpolation instead  
        if (std::abs(cos_theta) >= 0.9995) {  
            return normalize(std::lerp(p, r ,t));  
        }  
  
         // Otherwise use spherical linear interpolation  
  
         // Choose the shortest path between them  
         if (cos_theta < 0) {  
             cos_theta *= -1;  
             r.x *= -1; r.y *= -1; r.z *= -1; r.w *= -1;  
         }  
  
         // Compute the angle and its sine  
         double theta = std::acos(cos_theta);  
         double sin_theta = std::sin(theta);  
  
         // Compute the weights for each quaternion  
         double w_p = std::sin((1 - t) * theta) / sin_theta;  
         double w_r = std::sin(t * theta) / sin_theta;  
  
         // Return the interpolated quaternion  
         return {w_p * p.x + w_r * r.x,  
                 w_p * p.y + w_r * r.y,  
                 w_p * p.z + w_r * r.z,  
                 w_p * p.w + w_r * r.w};  
     }  
```

---

## Comment 3

> Username: zajo  
> Created at: 2023-02-26 20:53:18 UTC  
> Url: https://github.com/boostorg/qvm/issues/43#issuecomment-1445464397  

Hang on, I'm investigating.

---

## Comment 4

> Username: zajo  
> Created at: 2023-02-26 21:35:58 UTC  
> Url: https://github.com/boostorg/qvm/issues/43#issuecomment-1445473526  

The issue is that when the angle between the two rotations is greater than pi (180 degrees), you might want to go through the short path or the long path on the sphere. Eigen always picks the short path, but with that approach the following QVM unit test fails: https://github.com/boostorg/qvm/blob/develop/test/slerp_test.cpp.  
  
This is the part that fails:  
  
```  
for( float a1=0; a1<6.28f; a1+=0.1f )  
    {  
    test_qvm::quaternion<Q1> const qx1=rotx_quat(a1);  
    test_qvm::quaternion<Q1> const qy1=roty_quat(a1);  
    test_qvm::quaternion<Q1> const qz1=rotz_quat(a1);  
    for( float a2=0; a2<6.28f; a2+=0.1f )  
        {  
        test_qvm::quaternion<Q1> const qx2=rotx_quat(a2);  
        test_qvm::quaternion<Q1> const qy2=roty_quat(a2);  
        test_qvm::quaternion<Q1> const qz2=rotz_quat(a2);  
        for( float t=0; t<1; t+=0.1f )  
            {  
            test_qvm::quaternion<Q1> const qx=rotx_quat(a1*(1-t)+a2*t);  
            test_qvm::quaternion<Q1> const qy=roty_quat(a1*(1-t)+a2*t);  
            test_qvm::quaternion<Q1> const qz=rotz_quat(a1*(1-t)+a2*t);  
            test_qvm::quaternion<Q1> const qsx=slerp(qx1,qx2,t);  
            test_qvm::quaternion<Q1> const qsy=slerp(qref(qy1),qy2,t);  
            test_qvm::quaternion<Q1> const qsz=slerp(qz1,qref(qz2),t);  
            BOOST_QVM_TEST_CLOSE(qx.a,qsx.a,0.001f);  
            BOOST_QVM_TEST_CLOSE(qy.a,qsy.a,0.001f);  
            BOOST_QVM_TEST_CLOSE(qz.a,qsz.a,0.001f);  
            }  
        }  
    }  
```  
  
Of course if the angles in this test are limited to pi (3.14) rather than 2*pi (6.28) the test succeeds with either implementation.  
  
I'm tempted to change slerp to work like Eigen's slerp, in part because Shoemake (the original implementation) requires that the angle is less than pi, but I wonder if that change will break some user code, although it doesn't break anything in my engine.  
  
I'll give it some more thought.

---

## Comment 5

> Username: zajo  
> Created at: 2023-02-27 02:43:15 UTC  
> Updated at: 2023-02-27 02:59:45 UTC  
> Url: https://github.com/boostorg/qvm/issues/43#issuecomment-1445599471  

I think in order to avoid breaking user code it is best to not change QVM. If you want qvm::slerp to always take the short path, negate one of the input quats if the dot product is negative. Here is your program with this check added:  
  
```  
#include <boost/qvm/quat.hpp>  
#include <boost/qvm/quat_operations.hpp>  
#include <boost/qvm/quat_access.hpp>  
#include <cstdio>  
  
int main()  
{  
    boost::qvm::quat<double> q1 = { 0.0005261316933462083, 0.01051085312843599, -0.0003732733078038091, 0.9999445513722205 };  
    boost::qvm::quat<double> q2 = { 0.006719932961540192, -0.008611214157320685, 0.003628245065324309, -0.9999337604709 };  
  
    auto r = slerp(dot(q1, q2) < 0 ? -q1 : q1, q2, 0.1891208547850313);  
  
    std::printf("%f, %f, %f, %f\n", S(r), X(r), Y(r), Z(r));  
}  
```  
  
It outputs:  
  
```  
0.000844, -0.010152, 0.000989, -0.999948  
```  
  
(Of course in this particular program the dot check is redundant, since it is always negative).

---

## Comment 6

> Username: haraldF  
> Created at: 2023-02-27 09:17:03 UTC  
> Updated at: 2023-02-27 09:21:58 UTC  
> Url: https://github.com/boostorg/qvm/issues/43#issuecomment-1445969115  

Thanks @zajo for looking into this. From what I understand, we should use the fast path if the angles are close together otherwise we rist numerical instability.  
  
While I appreciate keeping backward compat, please understand that this has severe impact on user's code.   
  
This is an example how slerp'ing a robot's path (consisting of poses and observations) with boost qvm looks like:  
  
![1181_268_1](https://user-images.githubusercontent.com/547273/221520909-ab3dcc04-0ba7-4da6-b82e-db09fe978170.png)  
  
This is how it looks like after the patch (or with any other library, confirmed with Apple's simd or Eigen or three.js):  
  
![1174_222_1](https://user-images.githubusercontent.com/547273/221521208-1fcce23d-7bc4-466c-aa8e-950f3af8a7a9.png)  
  
Notice the bump in the first image.  
  
Thanks to your help I now know the workaround (unfortunately with a drop in performance, because the dot product will be computed twice). However, any new code will hit this issue.  
  
I'd rather go for correct than backward compatibility, and one could argue that old code will perform better with less visual glitches if we apply the patch.

---

## Comment 7

> Username: haraldF  
> Created at: 2023-02-27 12:27:05 UTC  
> Url: https://github.com/boostorg/qvm/issues/43#issuecomment-1446242495  

FYI - three.js also negates the quaternion if the dot product is negative: https://github.com/mrdoob/three.js/blob/dc9570226cf8e541cc71dfebf16f19935dabb74d/src/math/Quaternion.js#L528

---

## Comment 8

> Username: zajo  
> Created at: 2023-02-27 18:11:21 UTC  
> Url: https://github.com/boostorg/qvm/issues/43#issuecomment-1446814165  

There are use cases for both definitions, consider slerping ever increasing angles, you'd get a discontinuity when crossing 180 degrees. It is simply a matter of convention.  
  
The safest thing to do is add a function `slerp2` which implements the short path behavior, so that `slerp` isn't changed. Would that be OK?

---

## Comment 9

> Username: haraldF  
> Created at: 2023-02-28 09:32:03 UTC  
> Url: https://github.com/boostorg/qvm/issues/43#issuecomment-1447852800  

@zajo I'd give it a more impressive name :) What's a feature that `slerp2` has over `slerp`? `smoothSlerp`? `symmetricSlerp`? `slerpLikeEigenDoes`?

---

## Comment 10

> Username: zajo  
> Created at: 2023-03-02 05:25:34 UTC  
> Url: https://github.com/boostorg/qvm/issues/43#issuecomment-1451320480  

Resolution:  
* Added two new functions, `slerp180` and `slerp360` to implement both behaviors.  
* `slerp` is retained for now but it is deprecated. It is implemented in terms of `slerp360`, in order to not break existing code.  
This is now pushed on `develop`, soon to be merged to `release` and it will be out with Boost 1.82.0.
