# #259 Add example to detect edges [Open]

> Username: sid19991  
> Created at: 2019-03-15 14:29:40 UTC  
> Updated at: 2022-06-03 06:52:39 UTC  
> Url: https://github.com/boostorg/gil/pull/259  

This commit adds a cpp file to example folder. The example demonstrats the use of one  
dimensional filter for detection of horizontal and vertical edges.  
  
### Tasklist  
  
- [ ] Review  
- [ ] Adjust for comments  
- ~~All CI builds and checks have passed~~ ATM, examples are not built by CI jobs

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-03-15 18:25:54 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/259#pullrequestreview-215188528  

Could you add a comment briefly explaining the algorithm; steps of the procedure, in words?

---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2019-03-15 18:28:06 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/259#pullrequestreview-215189421  

📁 example/simple_filter.cpp

```diff
   6 |+ 
   7 |+ 
   8 |+ int main() {
```

> Username: mloskot  
> Created_at: 2019-03-15 18:27:58 UTC  
> Updated_at: 2019-03-22 10:48:40 UTC  
> Url: https://github.com/boostorg/gil/pull/259#discussion_r266100888  

Would it be possible to accept input image as command line argument?


---

## Review 3 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-03-17 23:47:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/259#pullrequestreview-215382927  

📁 example/simple_filter.cpp

```diff
  12 |+         printf("Usage: simple_filter <image_path>");
  13 |+         return 0;
  14 |+     }
```

> Username: stefanseefeld  
> Created_at: 2019-03-17 23:47:46 UTC  
> Updated_at: 2019-03-22 10:48:40 UTC  
> Url: https://github.com/boostorg/gil/pull/259#discussion_r266266743  

Please use 'std::cerr' rather than `printf`, and return `-1` to indicate the usage error.

> Username: mloskot  
> Created_at: 2019-03-18 09:17:44 UTC  
> Updated_at: 2019-03-22 10:48:40 UTC  
> Url: https://github.com/boostorg/gil/pull/259#discussion_r266348989  

I'd also allow to use the C standard macros https://en.cppreference.com/w/cpp/utility/program/EXIT_status


---

## Review 4 [Changes requested]

> Username: mloskot  
> Created_at: 2019-03-18 09:25:09 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/259#pullrequestreview-215488483  

📁 example/simple_filter.cpp

```diff
  27 |+     // making resizable kernel object
  28 |+     kernel_1d<float> kernel2(sharpen_1,3,1);
  29 |+     //compute horizontal edges
```

> Username: mloskot  
> Created_at: 2019-03-18 09:25:03 UTC  
> Updated_at: 2019-03-22 10:48:40 UTC  
> Url: https://github.com/boostorg/gil/pull/259#discussion_r266351545  

The comment seems to be repeating what the line of C++ code shows.  
It rather should explain purpose of computing those edges.  
  
Imagine I know very little about image processing and I read your code for the first time, do I have a chance to learn what algorithm your code implements?   
  
To me, you should give a comment explaining the algorithm, what it does, what are the steps.


---

## Review 5 [Commented]

> Username: mloskot  
> Created_at: 2019-03-18 09:26:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/259#pullrequestreview-215489041  

📁 example/simple_filter.cpp

```diff
  31 |+     //compute vertical edges
  32 |+     convolve_cols<gray32f_pixel_t>(color_converted_view<gray8_pixel_t>(const_view(img)),kernel2,view(convolved2));
  33 |+     //save horizontal edges
```

> Username: mloskot  
> Created_at: 2019-03-18 09:26:10 UTC  
> Updated_at: 2019-03-22 10:48:40 UTC  
> Url: https://github.com/boostorg/gil/pull/259#discussion_r266351995  

`save...` just repeats what the self-descriptive `write_view` indicates.  
Such comments repeating the actual code are redundant.


---

## Comment 6

> Username: mloskot  
> Created_at: 2019-04-28 01:10:55 UTC  
> Url: https://github.com/boostorg/gil/pull/259#issuecomment-487331507  

@stefanseefeld Shall we add those as they are (optionally, with minor stylistic refactoring) to `example/` folder? I think, it's never too many examples showing how to actually use GIL. What do you think?

---
