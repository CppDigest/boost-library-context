# #652 - add reference frame as tag for positional vectors [Open]

> Username: ferdymercury  
> Created at: 2019-12-13 18:27:47 UTC  
> Updated at: 2019-12-31 19:57:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/652  

A nice addition to this library would be to define different Reference Frames in the same coordinate system type, e.g. cartesian. See here rationale:  
  
https://stackoverflow.com/questions/34670464/how-can-i-add-coordinate-system-reference-frame-information-to-my-data-to-avoi  
  
Similar strategies are already implemented in the SymPy library (Python) and in CERN's ROOT (C++), but I haven't found it in Boost, and might be an interesting feature to have taking profit of the boost/geometry formalism. It could be backward compatible, in the sense that if one does not define it, then it uses a DefaultReferenceFrame as template default parameter.  
  
http://docs.sympy.org/0.7.2/modules/physics/mechanics/vectors.html  
http://project-mathlibs.web.cern.ch/project-mathlibs/sw/html/Vector.html

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-12-28 17:06:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/652#issuecomment-569433350  

Could you elaborate and maybe show some example?  
  
Currently a coordinate system of a geometry is defined in compile-time as a part of the corresponding point type. Technically speaking `bg::traits::coordinate_system` has to be specialized for a point type and `bg::traits::point_type` has to be specialized for higher-level geometry type. Is this something you are talking about?  
  
In addition to that we allow passing geometries of any coordinate system as long as a user explicitly passes a strategy which defines the coordinate system in which an operation is performed (e.g. intersection or relate). This is by design.  
  
With that said we sometimes allow passing any type into a function, e.g. any point type into vector algebra utilities like `dot` but these are only utilities used internally.

---

## Comment 2

> Username: ferdymercury  
> Created at: 2019-12-29 22:05:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/652#issuecomment-569546015  

This would be helpful too https://github.com/boostorg/geometry/issues/655

---

## Comment 3

> Username: ferdymercury  
> Created at: 2019-12-29 22:25:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/652#issuecomment-569547401  

Assume I have a 3D coordinate system A with a point P (1/5/-3). Now, another observer is sitting at a 90 degree angle with respect to observer A. In his own coordinate system, point P will be at (5/-1/3). The coordinates in each frame of reference are related by an affine transform matrix M. Ideally, for safe programming, one would leave all the matrix conversions implicit by doing something like:  
  
```  
auto A = frame_of_reference("A")  
auto B = frame_of_reference("B")  
auto M = affine_matrix(0, -1, 0, 1, 0, 0, 0,0,1)  
Boost::set_conversion_rule(A, B, M)  
auto P = Point3d(1,5,2,A)  
cout << P.in_frame('B');  
auto Q = Point3d(2,3,5,B)  
auto V =  P - Q;//automatically do subtraction in same frame of reference, applying internally conversion matrix.  
```

---

## Comment 4

> Username: ferdymercury  
> Created at: 2019-12-29 22:32:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/652#issuecomment-569547876  

This is how it is done in Python:  
  
```  
from sympy.physics.vector import ReferenceFrame, express  
from mpmath import radians  
  
A = ReferenceFrame('A')  
B = ReferenceFrame('B')  
  
# define some relationship between the two systems like a rotation*  
B.orient(A, 'Axis', [radians(90), A.z])  
  
# define a vector in frame A  
vector = [1, 0, 0]  
vector_inA = vector[0]*A.x + vector[1]*A.y + vector[2]*A.z  
  
print vector_inA  
# >A.x  
  
# determine the vector coordinates in frame B  
vector_inB = express(vector_inA, B)  
  
print vector_inA  
# >6.12323399573677e-17*B.x - B.y  
```

---

## Comment 5

> Username: awulkiew  
> Created at: 2019-12-29 23:49:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/652#issuecomment-569553194  

In case you didn't know. It is possible to create `matrix_transformer` [1] and pass it into `bg::transform()` [2]. It is also possible to pass corresponding Boost.QVM [3] matrix into `matrix_transformer` so you can use utilities from this library while calculating transformations. Like this:  
  
```  
namespace bg = boost::geometry;  
namespace bgm = boost::geometry::model;  
namespace bgst = boost::geometry::strategy::transform;  
namespace qvm = boost::qvm;  
  
qvm::mat<double, 4, 4> ma = qvm::identity_mat<double, 4>();  
qvm::mat<double, 4, 4> mb = ma;  
qvm::rotate_z(mb, 90.0 * bg::math::d2r<double>());  
  
bgm::point<double, 3, bg::cs::cartesian> p{1, 0, 0}; // or any other geometry  
  
bgm::point<double, 3, bg::cs::cartesian> pa, pb;  
bg::transform(p, pa, bgst::matrix_transformer<double, 3, 3>(ma));  
bg::transform(p, pb, bgst::matrix_transformer<double, 3, 3>(mb));  
```  
  
So frame of reference is not expressed directly but the functionality is there. Is this not enough in your opinion?  
  
If we wanted to automatically do frame of reference computation in various algorithms (like `difference` or `relate`) we'd have to pass frame of reference together with geometries, maybe even store it in a geometry itself. This would add unnecessary complexity IMO. I'd rather leave transformations to the users.  
  
Btw, vector algebra is not really the scope of Boost.Geometry. Boost.QVM seems to be a better library for the feature you propose.  
  
Or am I missing something?  
  
[1] https://www.boost.org/doc/libs/1_72_0/libs/geometry/doc/html/geometry/reference/strategies/strategy_transform_matrix_transformer.html  
[2] https://www.boost.org/doc/libs/1_72_0/libs/geometry/doc/html/geometry/reference/algorithms/transform/transform_3_with_strategy.html  
[3] https://www.boost.org/libs/qvm

---

## Comment 6

> Username: ferdymercury  
> Created at: 2019-12-31 19:48:42 UTC  
> Updated at: 2019-12-31 19:57:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/652#issuecomment-569981312  

Thanks for the reply and the hints. I didn't know about QVM but had used before Qt, which has Qquaternion and 4x4 matrices. However, my feature suggestion goes rather along the line of how to store information in a self contained object, rather than leaving to the programmer to do it correctly in every operation. In other ways, to have a good class that manages it.  
  
From the physics point of view, a mathematical point 1/2/5 has no meaning. It needs a reference frame definition, and also units. Boost.Units takes care of the second, but the first issue is still up to the programmer. Sometimes I have three reference frames in different parts of the code and it is error prone to get all transformations right. For example, in SymPy, you get rid of this problem quickly.  
  
That said, I am not sure if Geometry is the best library to incorporate it, but Ithought that because cartesian and spherical equivalencies are defined, there could be an analogous architecture among reference frames.  
  
Maybe it could be done by composition to not make the base classes any more complicate:  
  
```  
PhysicsPoint{  
Point3 p;  
ReferenceFrameTag t;  
ReferenceFrames* r;  
Point3 express_in(ReferenceFrameTag n);//takes care of conversion  
};  
ReferenceFrames rf("A","B");  
rf.set_rule("A","B",4x4matrix);  
Point3 p(1;5;2);  
PhysicsPoint Pp(p,rf, "A");  
cout << Pp.express_in("B");  
```  
And then, the difference could be sth like:  
```  
PhysicsPoint difference(PhysicsPoint p1, PhysicsPoint p2){  
Point3 dif = boost.difference(p1.p, p2.express_in(p1.t));  
return {dif, p1.t};  
}  
```  
So basically a higher level class wrapping more basic operations.
