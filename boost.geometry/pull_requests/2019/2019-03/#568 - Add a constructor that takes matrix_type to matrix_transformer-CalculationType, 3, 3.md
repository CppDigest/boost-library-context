# #568 Add a constructor that takes matrix_type to matrix_transformer<CalculationType, 3, 3> [Merged]

> Username: tinko92  
> Created at: 2019-03-12 22:39:20 UTC  
> Updated at: 2019-07-01 14:19:43 UTC  
> Merged at: 2019-07-01 14:19:43 UTC  
> Closed at: 2019-07-01 14:19:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/568  

The example file 06_b_transformation_example contains this useful example for composing matrix_transformers  
`matrix_transformer<double, 2, 2> combined(rotate.matrix() * translate.matrix());`.  
  
The analogue of this for matrix_transformer<CalculationType, 3, 3> causes a compiler error because the corresponding constructor is missing in the 3d case. This pull request is designed to fix this by adding the constructor   
`    inline matrix_transformer(matrix_type const& matrix)  
        : m_matrix(matrix)  
    {}`  
to matrix_transformer<CalculationType, 3, 3>.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2019-03-13 13:46:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/568#issuecomment-472428323  

I think it's a good idea.  
  
If needed this could even be expanded in the future (additional set of constructors added) to allow any `Matrix` type adapted to QVM Matrix concept.  
  
Btw, 4 commits are not needed for this but with the magic of GitHub I can squash them while merging. Is that ok or would you like to do this by yourself?

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2019-03-13 13:50:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/568#pullrequestreview-213961057  

Thanks! I am ok with merging.

📁 include/boost/geometry/strategies/transform/matrix_transformers.hpp

```diff
 132 | 
 133 |+     inline matrix_transformer(matrix_type const& matrix)
 134 |+         : matrix_transformer<CalculationType, 2,2>(matrix)
```

> Username: vissarion  
> Created_at: 2019-03-13 13:49:58 UTC  
> Updated_at: 2019-03-13 13:50:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/568#discussion_r265134875  

2,2 --> 2, 2 (with a space)


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2019-03-13 14:15:46 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/568#pullrequestreview-213977469  

Thanks for this! I'm OK

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2019-03-13 14:56:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/568#issuecomment-472457418  

Milestone is set to 1.70 - but will it be merged?   
1.70 opens for bugfixes today. This is a minor code change, but actually not a bugfix - though you might expect that it behaves the same as its companion, and then it is a bug

---

## Comment 5

> Username: awulkiew  
> Created_at: 2019-03-13 16:50:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/568#issuecomment-472508857  

I'd wait and merge it after the release, so target 1.71.

---

## Comment 6

> Username: tinko92  
> Created_at: 2019-03-13 17:25:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/568#issuecomment-472524208  

Thank you very much for the quick feedback.  
  
@awulkiew Sorry, for the four commits, I messed it up on the first try and only noticed later, I'll make sure to have cleaner commit histories on my PRs in the future and I'll also look into the magic of Github.   
  
I also agree that it could be expanded (and I'd have a use case for an expanded version of this in my code), but because these changes would be less trivial, I'll present my case for them in an Issue first.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2019-03-13 18:46:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/568#issuecomment-472555695  

Yep, let's wait then, updated milestone.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2019-07-01 14:17:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/568#issuecomment-507284027  

Thanks!

---
