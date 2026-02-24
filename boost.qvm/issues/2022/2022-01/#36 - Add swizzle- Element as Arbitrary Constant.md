# #36 - Add swizzle: Element as Arbitrary Constant [Closed]

> Username: stellarpower  
> Created at: 2022-01-17 18:13:23 UTC  
> Updated at: 2024-02-05 02:29:53 UTC  
> Closed at: 2024-02-05 02:29:53 UTC  
> Url: https://github.com/boostorg/qvm/issues/36  

We have support for a swizzle that adds a new dimension, with that dimension's element set at either 0 or 1.  
  
E.g. `XYZ1`, `X0XX`, etc.  
  
It would be useful to have a swizzle that allowed an arbitrary constant in a similar fashion:  
  
E.g. the above would be equialent to `XYZN<1.0>`, `XNXX<0.0>`, etc., or, for runtime access, `XYZN(1.0)`, `XNXX(0.0)`  
  
  
This could facilitate filling-in single leements as needed for passing ot a function:  
  
```  
vec<double, 2> my2DVector = anObject->someScreenCoördinates();  
// The object is known to be on a plane at z = arbitraryZ;  
double arbitraryZ = A<2>(anObject->plane.location);  
auto result = some3Dfunction(XYN(arbitraryZ)(my2DVector));  
```  
I believe currently ,the long way would be required:  
```  
vec<double, 2> my2DVector = anObject->someScreenCoördinates();  
// The object is known to be on a plane at z = arbitraryZ;  
double arbitraryZ = A<2>(anObject->plane.location);  
vec<double, 3> my3DVector{ A<0>(my2DVector), A<1>(my2DVector), arbitraryZ}  
auto result = some3Dfunction(my3DVector);  
```

---

## Comment 1

> Username: zajo  
> Created at: 2022-01-18 04:25:50 UTC  
> Url: https://github.com/boostorg/qvm/issues/36#issuecomment-1015060562  

What implementation do you have in mind?

---

## Comment 2

> Username: stellarpower  
> Created at: 2022-01-18 11:02:28 UTC  
> Url: https://github.com/boostorg/qvm/issues/36#issuecomment-1015301419  

I haven't looked at the implementation of the existing swizzles yet, I was presuming these must feature a constant somewhere, which could be replaced with the one supplied to the swizzle. If necessary I guess e.g. ZXZ1 could be rewritten to call ZXZN<1>.  
  
It might be necessary to work around some issues if the underlying storage type is floating or integral. In some unrelated code yesterday, I thought that for  
`template<double D> class C{};`  
trying to instantiate with an integral constant:  
`C<1>::...`  
the compiler ought to coërce the number to a double, knowing that there is no other option, however it didn't and complained to me that I'd given it the wrong sort of value-argument. Not sure what the standard says but I'll just assume g++ is compliant in this regard.  
  
We thus might need to be careful, as whilst I'm guessing the vast majority of qvm users will be using a floating-point type, there may be some use cases for something like screen co-ordinates constrained to integers. A simple cast to the detected scalar_type ought to do it and I'm guessing there may already be some workarounds like this in the codebase if a similar niggle comes up.

---

## Comment 3

> Username: federkamm  
> Created at: 2023-06-06 01:46:23 UTC  
> Url: https://github.com/boostorg/qvm/issues/36#issuecomment-1577770305  

How about  
  
    vec<double, 2> my2DVector = anObject->someScreenCoordinates();  
    // The object is known to be on a plane at z = arbitraryZ;  
    double arbitraryZ = A<2>(anObject->plane.location);  
    auto result = some3Dfunction(XY0(my2DVector) + _00X(arbitraryZ));  
  
or  
  
    auto myPlane = anObject->plane.location;  
    auto result = some3Dfunction(XY0(my2DVector) + _00Z(myPlane));
