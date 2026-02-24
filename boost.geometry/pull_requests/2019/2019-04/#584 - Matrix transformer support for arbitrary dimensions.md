# #584 Matrix transformer support for arbitrary dimensions [Merged]

> Username: tinko92  
> Created at: 2019-04-12 11:52:26 UTC  
> Updated at: 2019-10-24 12:13:17 UTC  
> Merged at: 2019-10-24 12:13:16 UTC  
> Closed at: 2019-10-24 12:13:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/584  

Hi, since the merge window for the next release has been opened, I wanted to again suggest a minor feature for matrix_transformer. The current implementation supports a specific subset of possible matrix_transformers, namely from 2d to 2d, 3d to 3d, and from 3d to 2d and implemented matrix multiplication for these three cases. It also supports the composition of transformers via matrix multiplication for transformers with the same dimension.  
  
I do have use-cases where I want to go from 2d back up to 3d. Rather than suggesting one more special case, I suggest this implementation that supports matrix_transformers for arbitrary dimensions, uses a single implementation of matrix multiplication (from Boost.QVM, which is already a dependency) and allows composition of matrix_transformers with different dimensions were applicable (e.g. we can compose transformations from 2d to 3d, from 3d to 3d and 3d to 2d in the appropriate order).  
  
This changes the visible behavior in one aspect: The matrix() function of the matrix_transformer from 3d to 2d will return 4x3 matrix (which are the necessary dimensions to support all affine transformations from 3d to 2d) rather than a 3x3 matrix. As far as I can see, this change caused no failures in the unit tests. Other than that, this should not change the interface in any way, except for increasing the number of supported parameters.  
  
I also added a unit test to test the new possibilities for composing transformations that are enabled by this change.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-01 14:25:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/584#pullrequestreview-256371059  

📁 include/boost/geometry/strategies/transform/matrix_transformers.hpp

```diff
  72 |+     std::size_t Dimension2
  73 |+ >
  74 |+ struct set_point_from_vec<CalculationType, P2, Dimension2, -1>
```

> Username: awulkiew  
> Created_at: 2019-07-01 14:25:51 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299068768  

`std::size_t` cannot hold -1. I think this may not compile with all compilers.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-01 14:27:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/584#pullrequestreview-256372002  

📁 include/boost/geometry/strategies/transform/matrix_transformers.hpp

```diff
  62 |+         typedef typename geometry::coordinate_type<P2>::type ct2;
  63 |+         set<Dimension2Count>(p2, boost::numeric_cast<ct2>(qvm::A<Dimension2Count>(p2temp)));
  64 |+         set_point_from_vec<CalculationType, P2, Dimension2, Dimension2Count-1>::apply(p2, p2temp);
```

> Username: awulkiew  
> Created_at: 2019-07-01 14:27:13 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299069537  

Typically we're counting from `0` to `Dimension2Count` not the other way around. Also then `-1`  is not needed.

> Username: tinko92  
> Created_at: 2019-07-01 17:33:32 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299146765  

Thanks, I will change the order of iteration.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-01 14:27:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/584#pullrequestreview-256372417  

📁 include/boost/geometry/strategies/transform/matrix_transformers.hpp

```diff
 133 |+         qvm::A<Dimension1>(p1temp) = 1;
 134 |+         p2temp = m_matrix * p1temp;
 135 |+         set_point_from_vec<ct, P2, Dimension2, Dimension2 - 1>::apply(p2, p2temp);
```

> Username: awulkiew  
> Created_at: 2019-07-01 14:27:51 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299069884  

If you were counting from 0 you wouldn't be forced to remember about `-1` here.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-01 14:42:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/584#pullrequestreview-256381824  

📁 include/boost/geometry/strategies/transform/matrix_transformers.hpp

```diff
  55 |+     std::size_t Dimension2Count
  56 |+ >
  57 |+ struct set_point_from_vec
```

> Username: awulkiew  
> Created_at: 2019-07-01 14:42:34 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299077130  

`CalculationType` is not really needed right? Nothing is calculated here.  
  
Typically the template arguments for such utility in Boost.Geometry would be:  
```  
template  
<  
    typename Point,  
    std::size_t Dimension = 0,  
    std::size_t DimensionCount = geometry::dimension<Point>::value  
>  
struct set_point_from_vec  
{  
    template <typename Vector>  
    static inline void apply(Point & p, Vector const& v)  
    {  
        typedef typename geometry::coordinate_type<Point>::type coord_t;  
        set<Dimension>(p, boost::numeric_cast<coord_t>(qvm::A<Dimension>(v)));  
        set_point_from_vec<Point, Dimension + 1>::apply(p, v);  
    }  
};  
```


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-01 14:47:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/584#pullrequestreview-256384578  

📁 include/boost/geometry/strategies/transform/matrix_transformers.hpp

```diff
 205 |     typedef CalculationType ct;
 120 |- 
 206 |+     typedef boost::qvm::mat<ct, 3, 4> matrix_type;
```

> Username: awulkiew  
> Created_at: 2019-07-01 14:46:59 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299079218  

Is this needed? It's a private type not used anywhere.


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-01 14:50:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/584#pullrequestreview-256387007  

📁 include/boost/geometry/strategies/transform/matrix_transformers.hpp

```diff
 202 | template <typename CalculationType>
 117 |- class matrix_transformer<CalculationType, 3, 2> : public matrix_transformer<CalculationType, 2, 2>
 203 |+ class matrix_transformer<CalculationType, 3, 2> : public detail::matrix_transformer::matrix_transformer<CalculationType, 3, 2>
```

> Username: awulkiew  
> Created_at: 2019-07-01 14:50:55 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299081186  

So before matrix 3x3 was stored internally, but now it will store 3x4. AFAIU the matrix stored internally should be square matrix representing transformation of the output which in this case is 3x3 since the output is 2d.

> Username: tinko92  
> Created_at: 2019-07-01 15:47:18 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299107620  

AFAIU the matrix_transformer is a general affine transformation for 2d to 2d or 3d to 3d, etc., i.e. it represents any composition of a translation and a linear map. For 3d to 2d consider the following transformation: (x, y, z) -> (x + 2z + a, y + 3z + b) represented by the augmented matrix  
1 0 2 a  
0 1 3 b  
0 0 0 1  
This is certainly a composition of a linear map (from 3d to 2d) and a translation (2d to 2d) but it cannot be represented as an augmented 3 x 3 matrix, so I think the general case requires (m+1)x(n+1) matrices to represent nd to md transformations.  
  
Besides generality, this has also the advantage that matrix transformers can be composed in the mathematically intuitive way by matrix multiplication, for example, given a 3d to 3d, a 3d to 2d and a 2d to 2d transformation the internal matrices can all be multiplied (in the correct order) and the result can be used to construct a single 3d to 2d transformation that represents the composition of the 3 original transformations.


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-01 14:54:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/584#pullrequestreview-256389224  

📁 include/boost/geometry/strategies/transform/matrix_transformers.hpp

```diff
 119 |+     typedef CalculationType ct;
 120 |+     typedef boost::qvm::mat<ct, Dimension2 + 1, Dimension1 + 1> matrix_type;
 121 |+     matrix_type m_matrix;
```

> Username: awulkiew  
> Created_at: 2019-07-01 14:54:09 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299082942  

AFAIU the matrix stored internally should always he square transformation matrix of the output dimension, so for 2d output the matrix should be 3x3, for 3d output 4x4. Am I right @barendgehrels @vissarion ?  
  
If that's correct then two DimensionX template arguments are not needed, you need only one.  
  
Also see the comment below about transformer from 3d to 2d.

> Username: vissarion  
> Created_at: 2019-07-02 09:53:20 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299400453  

If I am not missing something, it seems that you need a 4x3 transformation matrix to map from 3d to 2d, so not always square.

> Username: awulkiew  
> Created_at: 2019-07-10 13:40:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r302070206  

AFAIU @barendgehrels was the original author so I'd prefer if he expressed his opinion thoughts regarding it assuming he has some. @tinko92 how badly would you like this to be released with 1.71?

> Username: barendgehrels  
> Created_at: 2019-07-10 15:04:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r302116319  

I need more time to review this. Changing matrix dimensions does not look good to me. Does it not break anything?  
I come back to this next week.

> Username: tinko92  
> Created_at: 2019-07-10 22:36:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r302300165  

@awulkiew Not so badly that the review would be rushed, I can use private patches in my codebase for another release cycle. I also understand that 1.71 has just been closed for major changes.  
  
@barendgehrels This change in matrix dimensions is, I think, the single change to visible behavior in this whole pull request (I hope I was sufficiently transparent about this in my initial pull request). It can break code that takes the matrix from a 3d to 2d transformer and specifically expects it to be 3 by 3. The method that returns the internal matrix is not documented, it is not covered by test cases but it is a public method and it is shown in an example.  
  
Here is my rationale for proposing this breaking change:  
The matrix transformers are the base class of rotation, scaling, and translation, they are applied as matrix transformation on homogenous coordinates, in other words: they represent affine transformations. Two things about this:  
  
1. Affine transformations can be composed via matrix multiplication, a useful property demonstrated in example 06b ( https://github.com/boostorg/geometry/blob/f60ce3691f5941855ea8b503629b4d509156bfeb/example/06_b_transformation_example.cpp#L147 ). Currently one could not compose a 3d->3d transformation with a 3d->2d transformation, because it is not sensible to multiply a 3x3 matrix and a 4x4 matrix. With these patches, it will work because it is possible to multiply a 3x4 matrix and a 4x4 matrix to get a 3x4 matrix like it makes sense to compose a 3d->3d transformation and a 3d->2d transformation to get a 3d->2d transformation.  
  
2. A 3x4 matrix is necessary to represent all possible affine transformations from 3d to 2d. The current implementation cannot represent, for example, the transformation: (x, y, z) -> (2x + z + 1, 3y + z + 1). My proposed implementation does support this case.

> Username: awulkiew  
> Created_at: 2019-10-19 15:44:12 UTC  
> Updated_at: 2019-10-19 15:44:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r336742243  

> It can break code that takes the matrix from a 3d to 2d transformer and specifically expects it to be 3 by 3.   
  
@tinko92 You're talking about the constructor taking `matrix_type` correct? If that's the case I wouldn't care about it that much since it's a new addition done by you which was released with 1.71. Before we only had constructors taking the elements of the matrix and for 3->2 case the constructor was not changed, it still takes 3x3=9 elements. So we are backward compatible.  
  
Would it make sense to add constructor taking 3x4=12 elements and for symmetry another one taking 3x3 matrix? It would be possible to support two generic matrix types having different sizes with `enable_if`.

> Username: tinko92  
> Created_at: 2019-10-23 22:15:19 UTC  
> Updated_at: 2019-10-24 09:38:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r338303907  

@awulkiew This is true for the constructor, but there is also this method: https://github.com/boostorg/geometry/blob/5a13eea5edbad79c9512dd66ac18e6c0b553b489/include/boost/geometry/strategies/transform/matrix_transformers.hpp#L111 . I think this one was available before 1.71 and it will return another matrix type after the change for 3->2 transformers.  
  
If you consider it sensible, I could definitely implement the additional constructors that you're proposing.

> Username: awulkiew  
> Created_at: 2019-10-24 12:12:11 UTC  
> Updated_at: 2019-10-24 12:12:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r338536593  

Right, so we have to merge ASAP and list it as a breaking change in release notes.


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-01 15:02:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/584#pullrequestreview-256394544  

📁 include/boost/geometry/strategies/transform/matrix_transformers.hpp

```diff
 132 |+         set_vec_from_point<ct, P1, Dimension1, Dimension1 - 1>::apply(p1, p1temp);
 133 |+         qvm::A<Dimension1>(p1temp) = 1;
 134 |+         p2temp = m_matrix * p1temp;
```

> Username: awulkiew  
> Created_at: 2019-07-01 15:02:02 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299087055  

Previously all of this code was "hardcoded" and then simply set e.g.:  
  
    ct p2x = c1 * qvm::A<0,0>(m_matrix) + c2 * qvm::A<0,1>(m_matrix) + qvm::A<0,2>(m_matrix);  
  
Have you checked if there is a difference in performance?  
  
Another thing is that maybe it would be possible to write a wrapper or a view for Boost.Geometry point, (holding a reference or pointer to Point) and adapter to QVM Vector concept. This could allow you to pass it into QVM without conversions. So maybe all of the code above could look like:  
  
    qvm_vector_view<P2> v2(p2);  
    v2 = m_matrix * qvm_vector_view<P1 const>(p1);

> Username: awulkiew  
> Created_at: 2019-07-01 15:22:05 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299096553  

Here is an example of such view type:  
  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/views/detail/indexed_point_view.hpp#L35  
  
This is a view for segments and boxes which is adapted to Point concept and allows to represent one of segment's endpoints or box corner.  
  
The difference would be that the view would be adapted to qvm vector concept like that:  
https://github.com/boostorg/qvm/blob/master/include/boost/qvm/vec.hpp#L38 .

> Username: tinko92  
> Created_at: 2019-07-01 17:03:22 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299136645  

Regarding performance: I did not create a benchmark back then, I remember checking the qvm implementation and seeing that it had similarly unrolled code ( https://github.com/boostorg/qvm/blob/master/include/boost/qvm/gen/mat_operations3.hpp#L1808 ). But now that you mention it, I am probably wasting execution time (and maybe 2*sizeof(coordinate_type) of stack space) by doing the full multiplication when the last row must always be one and the last column can be added directly. I will do some measurements, see how it compares when I use the deleted column/deleted row views.   
  
Regarding a vector view: I did consider such an approach, but I was unsure how to exactly fit the boost::numeric_cast into that because qvm seems to require write access through a returned reference, rather than passing the value to be written to some user-defined function where I could do the numeric_cast. Because I didn't want to change behavior if possible and I was concerned I could overlook some effect of numeric_cast, I did it this way and first calculated all entries, then wrote them to the result vector with the numeric_cast.

> Username: awulkiew  
> Created_at: 2019-07-02 02:28:17 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299281868  

> by doing the full multiplication  
  
The easiest solution is to keep the hardcoded operations as they were implemented in specializations and use your new implementation for other/higher dimensions. This actually would solve both problems (unneeded operations and temporary vectors)  
  
>Regarding a vector view  
  
Right, I didn't check this well enough, QVM returns mutable reference and Geometry uses set function. So indeed representing Point as Vector may be not possible. Theoretically it would be possible to return non-true reference (like `boost::refference_wrapper`) but I wouldn't be so sure that it would work with QVM. I'd rather assume it requires true references. So yes, temporaries may be the best solution.

> Username: tinko92  
> Created_at: 2019-07-02 12:09:11 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299448875  

Ok. In my latest commit I've reintroduced the hardcoded operations with the following changes:  
- the 3d to 2d transformations now considers the z coordinate in the input as discussed on the matter of matrix dimensions for 3d to 2d.  
- I removed some temporaries in 2d to 2d that were not included in the original implementation of 3d to 3d.  
  
I previously didn't notice, that only dimension_greater_equal is asserted in https://github.com/boostorg/geometry/blob/5a13eea5edbad79c9512dd66ac18e6c0b553b489/include/boost/geometry/strategies/transform/matrix_transformers.hpp#L95 . I suppose this is meant to support the case that, for example, a 2d to 2d transformation is applied to 3d points that leaves the z coordinate untouched? In case this is intended, I changed the asserts into only asserting greater or equal.


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-01 15:03:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/584#pullrequestreview-256395249  

📁 include/boost/geometry/strategies/transform/matrix_transformers.hpp

```diff
 127 |+     {
 128 |+         assert_dimension<P1,Dimension1>();
 129 |+         assert_dimension<P2,Dimension2>();
```

> Username: awulkiew  
> Created_at: 2019-07-01 15:03:04 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299087554  

A better place for assertions would probably be exactly where the coordinates are accessed, e.g. in `set_point_from_vec`.

> Username: tinko92  
> Created_at: 2019-07-01 17:32:29 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299146390  

I did not spend much thought on the locations of these assertions when I wrote this, but after thinking about it now I think that if the user sees the asserts in that location failing, it would be more instructive than in the implementation details because as they are now, they communicate "you have the wrong transformer, it needs to match your points' dimension like in this assertion." (or this is what I hope they communicate).


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-01 15:04:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/584#pullrequestreview-256396561  

📁 test/strategies/Jamfile.v2

```diff
  39 |     [ run transform_cs.cpp                   : : : : strategies_transform_cs ]
  40 |     [ run transformer.cpp                    : : : : strategies_transformer ]
  41 |+     [ run matrix_transformer.cpp             : : : : matrix_strategies_transformer ]
```

> Username: awulkiew  
> Created_at: 2019-07-01 15:04:50 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299088594  

`strategies_matrix_transformer`  
  
FYI, the whole point of prefixing all of the tests with the same word is to allow our regression tool to filter tests and put them in separate sections in the regression matrix, see:  
https://www.boost.org/development/tests/develop/developer/geometry.html

> Username: tinko92  
> Created_at: 2019-07-01 17:34:01 UTC  
> Updated_at: 2019-07-02 21:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#discussion_r299146940  

Thanks for that pointer, I was unaware of that and I will fix it.


---

## Comment 11

> Username: awulkiew  
> Created_at: 2019-07-01 15:06:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#issuecomment-507304134  

It seems that after merging https://github.com/boostorg/geometry/pull/568 there are some conflicts.

---

## Comment 12

> Username: tinko92  
> Created_at: 2019-07-01 15:10:25 UTC  
> Updated_at: 2019-07-01 15:14:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#issuecomment-507305537  

I based both independently on develop. Thanks for the comments, I will try to rebase on the current develop branch and resolve all comments today.

---

## Comment 13

> Username: tinko92  
> Created_at: 2019-07-01 23:15:17 UTC  
> Updated_at: 2019-07-01 23:24:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#issuecomment-507458324  

I resolved the merge conflicts, I've run a simple performance test (10^8 rotations, my branch vs develop) and saw no change in performance, and I hope I resolved all comments, that do not depend on questions that I voiced in the comments.

---

## Comment 14

> Username: awulkiew  
> Created_at: 2019-10-19 15:44:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#issuecomment-544162522  

I was thinking about merging this PR. Do you have any objections @barendgehrels @vissarion ?

---

## Review 15 [Approved]

> Username: vissarion  
> Created_at: 2019-10-21 08:13:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/584#pullrequestreview-304389927  

I am ok with merging.

---

## Comment 16

> Username: awulkiew  
> Created_at: 2019-10-24 12:13:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/584#issuecomment-545889003  

Thanks Tinko for this improvement!

---
