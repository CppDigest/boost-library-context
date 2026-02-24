# #185 Fix conflicting definitions from io/dynamic_io_new.hpp and toolbox/dynamic_images.hpp [Merged]

> Username: mloskot  
> Created at: 2018-12-11 19:59:27 UTC  
> Updated at: 2018-12-12 16:59:09 UTC  
> Merged at: 2018-12-12 16:58:58 UTC  
> Closed at: 2018-12-12 16:58:58 UTC  
> Url: https://github.com/boostorg/gil/pull/185  

The problem appears to be due to the same definitions copied from one part of the library to the other.  
The definitions have been shuffled to fix the compilation, but _purely based on searching the code for what is used where, thus without confidence where those belong by **author's intention**_.  
  
Fixes #183  
  
### Tasklist  
  
- [x] Add test case(s) - see #184  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
-----  
  
1. `extension/toolbox/dynamic_images.hpp`  - removed `construct_matched` which is defined in `io/dynamic_io_new.hpp` and it is not used anywhere in `extension/toolbox/*.hpp` , but only in `extension/io`.  
  
![image](https://user-images.githubusercontent.com/80741/49827479-b2635000-fd89-11e8-879c-21cf366c1016.png)  
  
  
2. `io/dynamic_io_new.hpp` - removed `any_image_*_t` structures which are defined and used in `extension/toolbox/dynamic_images.hpp`, but nowhere else.  
  
![image](https://user-images.githubusercontent.com/80741/49827551-df176780-fd89-11e8-9a43-809f90e68656.png)

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-12-11 23:19:53 UTC  
> Url: https://github.com/boostorg/gil/pull/185#issuecomment-446400384  

@stefanseefeld this one is now ready to review.  
  
CI buils are still ongoing for the boostorg, but my personal one has finished green https://travis-ci.org/mloskot/gil/builds/466699298

---

## Comment 2

> Username: sdebionne  
> Created_at: 2018-12-12 09:14:30 UTC  
> Url: https://github.com/boostorg/gil/pull/185#issuecomment-446516062  

Since the old version of `io/dynamic_io_new.hpp` has been removed, should  `io/dynamic_io_new.hpp` be `io/dynamic_io.hpp` really?  
I fall into this trap to, and took the opposite direction, that is removing `dynamic_io_new.hpp` and including the `toolbox/dynamic_images.hpp` where needed.

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-12-12 09:20:12 UTC  
> Updated_at: 2018-12-12 09:20:41 UTC  
> Url: https://github.com/boostorg/gil/pull/185#issuecomment-446517861  

> Since the old version of io/dynamic_io_new.hpp has been removed, should io/dynamic_io_new.hpp be io/dynamic_io.hpp really?  
  
Possibly, or even `io/dynamic.hpp`.  
  
> removing dynamic_io_new.hpp and including the toolbox/dynamic_images.hpp where needed.  
  
That _seems_ like an arguable direction.  The `io/dynamic_io_new.hpp` is a core header. The toolbox is an extension. Since extensions are optional, even those officially released, and nothing in the core should depend on extensions (there is one or two places where this policy has been broken though, I think, to be fixed).

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-12-12 16:51:21 UTC  
> Url: https://github.com/boostorg/gil/pull/185#issuecomment-446659278  

@stefanseefeld Thanks for the review.  
  
@sdebionne Do you agree see any problems; have any objections against merging this PR? e.g. if you think it would seriously break things on your end of GIL usage.

---

## Comment 5

> Username: sdebionne  
> Created_at: 2018-12-12 16:55:32 UTC  
> Url: https://github.com/boostorg/gil/pull/185#issuecomment-446660811  

Nope, nothing serious, go ahead! Thanks for asking!

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-12-12 16:58:02 UTC  
> Updated_at: 2018-12-12 16:58:23 UTC  
> Url: https://github.com/boostorg/gil/pull/185#issuecomment-446661795  

Great, merging.  
  
...and praying

---
