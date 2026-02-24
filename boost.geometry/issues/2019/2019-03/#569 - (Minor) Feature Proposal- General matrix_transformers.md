# #569 - (Minor) Feature Proposal: General matrix_transformers [Closed]

> Username: tinko92  
> Created at: 2019-03-14 01:21:22 UTC  
> Updated at: 2019-11-06 15:51:46 UTC  
> Closed at: 2019-11-06 15:51:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/569  

I would like to propose a minor feature. In my recent pull request, which added constructors from a matrix to matrix_transformers<CalculationType, 3, 2> and matrix_transformers<CalculationType, 3, 3>, it was pointed out that this could be expanded and I agree. The matrix_transformer-Class can be generalized to handle arbitrary input and output dimensions by using a (Dimension_output + 1) x (Dimension_input + 1) matrix and multiplying it with the augmented vector as it is done for the specializations that are already implemented.  
  
I drafted an implementation to illustrate that idea:  
  
https://github.com/tinko92/geometry/commit/97cf49bc539a435701bd7f79a338489108a7b8db (Edit: After some consideration I changed my implementation to use qvm matrix-vector-multiplication instead)  
  
Here are the issues I see:  
  
This would change observable behavior for code that compiles today in one aspect, as far as I can see, namely that matrix_transformer<CalculationType, 3, 2>::matrix() would return a 4 x 3 matrix rather than a 3 x 3 matrix like it does now and that the constructor takes a 4 x 3 matrix (but a version with a constructor taking a 3 x 3 matrix has not been released yet).  
  
This is necessary, though, to be able to handle all affine transformations from 3d to 2d. I also suggest to do it this way to be consistent and because it would allow to naturally compose matrix_transformer between different dimensions through matrix multiplication (which will always produce a matrix of appropriate dimensions).  
  
This proposal would remove two implementations of matrix-vector multiplication in favor of one implementation that handles arbitrary matrix sizes.   
  
The advantage of this implementation lies, in my opinion, in the fact that it generalizes existing functionality with almost no changes to current behavior and, as far as I can see, no changes to documented behavior or behavior shown in tests or examples. I'd also argue that this feature allows composition of matrix_transformers in a manner that is mathematically intuitive.

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-03-16 17:20:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/569#issuecomment-473567040  

To be clear. In the PR you proposed I suggested adding ctors taking any matrix type (adapted to QVM matrix concept) but of the same dimensions as the internal representation of transformation. The reason being the case when someone uses his own transf. matrix type. Then he'd only be forced to adapt it and the Geometry transformer would work.  
  
I don't know if handling of arbitrary dimensions is needed because QVM allows to create views (or whatever they're called) of matrices without creating regular matrix objects.

---

## Comment 2

> Username: tinko92  
> Created at: 2019-03-16 17:53:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/569#issuecomment-473569855  

Thank you for your feedback. The constructors I added in my draft support all matrices that are adapted to QVM, as you suggested. They do not handle matrices of incorrect dimensions, only those that match the internal representation.  
  
Beyond your suggestion, though, my proposal handles arbitrary dimensions when it comes to the kind of points that can be transformed into each other, such as transformations from 4D points to 3D points or 2D points to 3D points and so on. The current implementation, if I understand it correctly, only handles 3D to 3D, 3D to 2D and 2D to 2D. The implementation I propose handles every case that QVM matrix multiplication can handle. It also removes two implementations of matrix multiplication for special cases in favor of using the matrix multiplication implemented in QVM for all cases.  
  
Additionally, the current implementation uses 3 by 3 matrices for transformations from 3D to 2D. This is insufficient to handle all affine transformations, 3 by 4 matrices are necessary for that. If I understand the current implementation correctly, it just drops the z-coordinate and then performs some affine transformation from 2D to 2D. For example, mapping (x, y, z) to (x + z + c, y + z + c) (where c is some constant) cannot be expressed by the current implementation, even though that is an affine transformation.  
  
With respect to the question of whether the handling of arbitrary point dimensions is needed: I drafted the suggestion because I had a use case that required 2D to 3D transformations. My use case involves taking faces of some 3D mesh with geometries on their faces, mapping them to the 2D planes to do some manipulations that are easier to implement when the face lies in the xy-plane and then map the resulting points back to the corresponding face in the 3D mesh.

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-11-06 15:51:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/569#issuecomment-550372151  

This was implemented by @tinko92 here: https://github.com/boostorg/geometry/pull/584
