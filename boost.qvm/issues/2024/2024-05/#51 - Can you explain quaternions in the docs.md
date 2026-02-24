# #51 - Can you explain quaternions in the docs? [Closed]

> Username: akrzemi1  
> Created at: 2024-05-11 19:54:54 UTC  
> Updated at: 2024-08-28 18:50:47 UTC  
> Closed at: 2024-08-28 18:50:47 UTC  
> Url: https://github.com/boostorg/qvm/issues/51  

I landed on this library docs when trying to learn what quarterions are for. Although the library has a vast documentation it doesn't explain what quaternions are and why they are used. When I was taught algebra for the purpose of computer graphics, vectors and matrices were on the table, but no quaternions. The examples show that I need to create a quaternion only to turn it into a rotation matrix. I am pretty sure there has to be more to it.  
  
Could I request an introduction section in the docs that would explain what quaternions are and why one would want to use them?

---

## Comment 1

> Username: zajo  
> Created at: 2024-05-11 20:32:47 UTC  
> Url: https://github.com/boostorg/qvm/issues/51#issuecomment-2106023088  

Quaternions were discovered in 1843 by Hamilton. For a long time they were studied by mathematicians but had no significant practical use. Today they're used anywhere you need to represent a 3D rotation. Matrices can represent much more than rotations, and so they're a lot less efficient than quaternions when all you need is a rotation.  
  
Robotics, computer graphics, physics, orbital mechanics -- anything that needs to work with rotations uses quaternions.  
  
That said, QVM doesn't explain the practical use of matrices and vectors in these fields, either. The problem is that this stuff can get very technical, and it isn't very helpful to just link the relevant Wikipedia page. What you need is a tutorial on, say, computer graphics, but that's beyond the scope of the library.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-05-11 22:23:28 UTC  
> Url: https://github.com/boostorg/qvm/issues/51#issuecomment-2106045386  

You are naturally right that explaining the whole theory behind quaterinions is not the job of this library. But I still believe a motivating example like this one would flatten the learning curve:  
  
```c++  
qvm::vec<double, 3> axis {{ 0.0, 1.0, 0.0}};  
double angle = (3.14159/2.0);  
qvm::quat<double> rot = qvm::rot_quat(axis, angle);  
qvm::vec<double, 3> in_vec {{1.0, 0.0, 0.0}};  
qvm::vec<double, 3> out_vec = rot * in_vec;  
assert(close(qvm::Z(out_vec), -1.0));  
```  
  
https://godbolt.org/z/czqETe7bj

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-05-12 20:02:32 UTC  
> Url: https://github.com/boostorg/qvm/issues/51#issuecomment-2106359624  

The link from Kostas is nice, and maybe it could be included in the docs:  
https://danceswithcode.net/engineeringnotes/quaternions/quaternions.html  
  
One thing that is unclear to me: is a rotation of a point by a queternion equivalent to multiplying a point by a quaternion? The referenced docs say that I have to perform two multiplications:  
  
```  
ans = inv(quat) * pt * quat;  
```  
  
But my test program says that I only need one multiplication:  
  
```c++  
qvm::vec<double, 3> axis {{ 1.0, 0.0, 1.0 }};  
double angle = std::numbers::pi / 2.0;  
qvm::quat<double> rot = qvm::rot_quat(axis, angle);  
qvm::vec<double, 3> in_vec {{1.0, 0.0, 0.0}};  
qvm::vec<double, 3> out_vec = rot * in_vec;  
   
assert(close(qvm::X(out_vec), 0.5));  
assert(close(qvm::Y(out_vec), std::numbers::sqrt2 / 2.0));  
assert(close(qvm::Z(out_vec), 0.5));  
```  
  
(full example: https://godbolt.org/z/efP97rfM7)  
  
I would appreciate if the docs clarified this.
