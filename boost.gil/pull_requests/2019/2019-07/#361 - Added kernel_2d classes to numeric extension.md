# #361 Added kernel_2d classes to numeric extension [Merged]

> Username: miralshah365  
> Created at: 2019-07-31 13:39:37 UTC  
> Updated at: 2019-10-24 08:08:16 UTC  
> Merged at: 2019-08-04 14:16:48 UTC  
> Closed at: 2019-08-04 14:16:48 UTC  
> Url: https://github.com/boostorg/gil/pull/361  

### Description  
for more detail check the reference(s)  
<!-- What does this pull request do? -->  
  
### References  
#356   
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [X] Add test case(s)  
- [x] Ensure all CI builds pass (AppVeyor ignored as per Gitter chat on [August 3, 2019 11:21 PM](https://gitter.im/boostorg/gil?at=5d45facbd7fc954750f3fe84))  
- [x] Review and approve (done in https://github.com/boostorg/gil/pull/361#pullrequestreview-270638311)

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-07-31 22:19:42 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/361#pullrequestreview-269289900  

After the first round of the review, I have three issues:  
  
1. The storage is not optimal (see https://github.com/boostorg/gil/pull/361#discussion_r309442814)  
2. The indexing is ambiguous (see https://github.com/boostorg/gil/pull/361#discussion_r309454819)  
3. The PR subject `2D kernel support added` suggests algorithms operating on 2D kernels have been also included, what does not seem to be the case. I'd suggest to change it to something like "Add 2D kernel definitions to numeric extension"

📁 include/boost/gil/extension/numeric/kernel.hpp

```diff
 249 |+ 
 250 |+ private:
 251 |+     std::size_t center_vertical_{ 0 }, center_horizontal_{ 0 };
```

> Username: mloskot  
> Created_at: 2019-07-31 21:20:52 UTC  
> Updated_at: 2019-08-03 21:17:20 UTC  
> Url: https://github.com/boostorg/gil/pull/361#discussion_r309437247  

Could you consider replacing the two variables with one aggregate?  
  
```  
using center_t = point<std::size_t>;  
...  
center_t center_;  
```

> Username: miralshah365  
> Created_at: 2019-08-03 18:36:05 UTC  
> Updated_at: 2019-08-03 21:17:20 UTC  
> Url: https://github.com/boostorg/gil/pull/361#discussion_r310359656  

done

---

📁 include/boost/gil/extension/numeric/kernel.hpp

```diff
 261 |+ >
 262 |+ class kernel_2d :
 263 |+     public detail::kernel_2d_adaptor<std::vector<std::vector<T, ColAllocator>, RowAllocator>>
```

> Username: mloskot  
> Created_at: 2019-07-31 21:36:56 UTC  
> Updated_at: 2019-08-03 21:17:20 UTC  
> Url: https://github.com/boostorg/gil/pull/361#discussion_r309442814  

Instead of `std::vector<std::vector<...>>`, would it be possible to use continuous memory for storage here? i.e.  just `std::vector<T>`.  
  
There is number of quirks and issues, including performance issues (dive into the discussion about [what are the Issues with a vector-of-vectors?](https://stackoverflow.com/q/38244435/151641))

> Username: miralshah365  
> Created_at: 2019-08-03 18:46:47 UTC  
> Updated_at: 2019-08-03 21:17:20 UTC  
> Url: https://github.com/boostorg/gil/pull/361#discussion_r310359950  

done


📁 test/extension/numeric/kernel.cpp

```diff
  53 |+     gil::kernel_2d<int> k(9, 4, 4);
  54 |+     BOOST_TEST(k.center_vertical() == 4);
  55 |+     BOOST_TEST(k.center_horizontal() == 4);
```

> Username: mloskot  
> Created_at: 2019-07-31 22:15:14 UTC  
> Updated_at: 2019-08-03 21:17:20 UTC  
> Url: https://github.com/boostorg/gil/pull/361#discussion_r309454819  

Given 2D kernel is a rectangular array of numbers, addressing element in the center using 1D indexing seems ambiguous. Especially that no fat comment is provided on that peculiarity and its rationale.  
  
Let's consider canonical form of a 3x3 kernel (symmetric, with center element in the center), then the indexing is as follows (column-major order):  
  
```  
| 0,0 | 1,0 | 2,0 |  
| 0,1 | 1,1 | 2,1 |  
| 0,2 | 1,2 | 2,2 |  
```  
  
Thus, most readers, I think, would have expected these:  
  
```cpp  
BOOST_TEST(k.center_vertical() == 1);  
BOOST_TEST(k.center_horizontal() == 1);  
```  
  
Alternative indexing convention often used for the 2D kernel is with center at `0,0` as explained:  
   
- http://www.songho.ca/dsp/convolution/convolution2d_example.html  
- http://www.songho.ca/dsp/convolution/convolution.html#convolution_2d  
  
```  
| -1,-1 | 0,-1 | 1,-1 |  
| -1, 0 | 0, 0 | 1, 0 |  
| -1, 1 | 0, 1 | 1, 1 |  
```  
  
The sizes up/down/left/right do not seem to be correct either.

> Username: mloskot  
> Created_at: 2019-08-01 20:22:50 UTC  
> Updated_at: 2019-08-03 21:17:20 UTC  
> Url: https://github.com/boostorg/gil/pull/361#discussion_r309880935  

@miralshah365 As I said on Gittter, you've probably read more image processing literature than I have, so you've seen many indexing conventions and should know better which is canonical or common.  
  
However, I can see why some prefer to refer to kernel center at `(0, 0)`, because  
- it is intuitive to have center at constant location (for any odd and square kernel)  
- it makes arithmetic to access a pixel neighbourhood easy, especially for the optimal linear storage of image data, for example, in `3x3` kernel:  
  
    ```  
    // 1 is left/right/up/down size from the center in 3x3 kernel  
    pixel_index = (pixel_x + kernel_i - 1) + image_width * (pixel_y + kernel_j - 1);  
    ```  
  
One image for thousand words 😄   
  
![image](https://user-images.githubusercontent.com/80741/62324158-2b462200-b4a9-11e9-988b-865745ba9f75.png)  
(Source: https://www.cs.utexas.edu/~theshark/courses/cs324e/lectures/cs324e-6.pdf)  
  
Obviously, the pattern becomes trickier for even kernels e.g. `4x4`

> Username: miralshah365  
> Created_at: 2019-08-03 18:46:40 UTC  
> Updated_at: 2019-08-03 21:17:20 UTC  
> Url: https://github.com/boostorg/gil/pull/361#discussion_r310359947  

> Let's consider canonical form of a 3x3 kernel (symmetric, with center element in the center), then the indexing is as follows (column-major order):  
>   
> | 0,0 | 1,0 | 2,0 |  
> | 0,1 | 1,1 | 2,1 |  
> | 0,2 | 1,2 | 2,2 |  
  
I chose to go with this style of indexing because it is easier to maintain and indexing will behave the same as any 2D array-like and I believe this will be less confusing for the user instead of remembering the different style of indexing.


---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2019-08-03 19:56:37 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/361#pullrequestreview-270471120  

@miralshah365 I added one extra request to replace assertions with exceptions. Other than that, it looks good to me. Once you make this change and CI-s pass, feel free to merge

📁 include/boost/gil/extension/numeric/kernel.hpp

```diff
 252 |+     {
 253 |+         BOOST_ASSERT(0 <= x && x < size());
 254 |+         BOOST_ASSERT(0 <= y && y < size());
```

> Username: mloskot  
> Created_at: 2019-08-03 19:53:53 UTC  
> Updated_at: 2019-08-03 21:17:20 UTC  
> Url: https://github.com/boostorg/gil/pull/361#discussion_r310361559  

@miralshah365 Instead of assertions, this should throw exceptional if x and y is put of range. See `std::vector::at` for reference and example

> Username: miralshah365  
> Created_at: 2019-08-03 20:34:01 UTC  
> Updated_at: 2019-08-03 21:17:20 UTC  
> Url: https://github.com/boostorg/gil/pull/361#discussion_r310362315  

done


---

## Review 3 [Commented]

> Username: mloskot  
> Created_at: 2019-08-05 08:59:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/361#pullrequestreview-270638311  

Since the request in https://github.com/boostorg/gil/pull/361#pullrequestreview-270471120 has been addressed, I'm happy to approve this.

---
