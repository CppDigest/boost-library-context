# #612 Add tests for checking boundary options in 1D convolution [Open]

> Username: meshtag  
> Created at: 2021-05-29 07:58:18 UTC  
> Updated at: 2025-09-05 19:11:41 UTC  
> Url: https://github.com/boostorg/gil/pull/612  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
This pull request intends to add unit tests for verifying boundary manipulation in `1D convolution`. Since these are tests(used for proving `correctness` of implementation), I have favoured readability/simplicity over performance(for example : All tests written inside `convolve.cpp` do not require a separate image to be created after row convolution. However, doing so greatly simplifies its structure and saves the reader from unnecessary trouble of understanding a complex manipulation).   
  
Flag `boundary_option::extend_padded` convolves the image assuming a padding around it. This leads to some unknown values of boundary pixels. For testing rest of the pixels(whose values can be predicted), I have deliberately made unknown values in `original_output_image` and `expected_image` equal. I have highlighted this with comments in the code.  
  
<!-- What does this pull request do? -->  
  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-05-29 08:56:49 UTC  
> Updated_at: 2025-09-05 19:11:41 UTC  
> Url: https://github.com/boostorg/gil/pull/612#issuecomment-850799021  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/612?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 79.28%. Comparing base ([`843ea37`](https://app.codecov.io/gh/boostorg/gil/commit/843ea374b69dc90b60d289e308c4e35319e8be58?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`368155c`](https://app.codecov.io/gh/boostorg/gil/commit/368155c9757283cc58af53f728d5bc58500878de?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 108 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #612      +/-   ##  
===========================================  
+ Coverage    78.76%   79.28%   +0.51%       
===========================================  
  Files          117      117                
  Lines         5030     5030                
===========================================  
+ Hits          3962     3988      +26       
+ Misses        1068     1042      -26       
```  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 2 [Changes requested]

> Username: simmplecoder  
> Created_at: 2021-05-30 19:13:58 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/612#pullrequestreview-671847337  

📁 test/extension/numeric/convolve.cpp

```diff
 146 |+         }
 147 |+ 
 148 |+         auto const kernel = fixture::create_kernel<channel_t>({0, 0, 0, 0, 0, 0, 1, 0, 0});
```

> Username: simmplecoder  
> Created_at: 2021-05-30 16:48:04 UTC  
> Updated_at: 2021-05-30 19:13:58 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r642101224  

I believe it would be better to give this kernel a name. `shift_by_two` perhaps?

---

📁 test/extension/numeric/convolve.cpp

```diff
 135 |+             auto img_it = img_view.row_begin(y);
 136 |+             auto img_expected_row_it = gil::view(img_expected_row).row_begin(y);
 137 |+             for (std::ptrdiff_t x = 2; x < img_view.width(); ++x)
```

> Username: simmplecoder  
> Created_at: 2021-05-30 16:53:29 UTC  
> Updated_at: 2021-05-30 19:13:58 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r642101828  

Giving the 2 a name would be great. I get it that it is the offset of 1 from the center of the kernel, right? Perhaps you could name it like `kernel_shift_offset` or something like that.

---

📁 test/extension/numeric/convolve.cpp

```diff
 136 |+             auto img_expected_row_it = gil::view(img_expected_row).row_begin(y);
 137 |+             for (std::ptrdiff_t x = 2; x < img_view.width(); ++x)
 138 |+                 img_expected_row_it[x] = img_it[x - 2];
```

> Username: simmplecoder  
> Created_at: 2021-05-30 17:51:01 UTC  
> Updated_at: 2021-05-30 19:13:58 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r642107976  

I believe it would be better to copy into `std::vector` and then use `std:::rotate` and write back to  actually tell what is the operation we are performing. Perhaps @lpranam and @mloskot will tip the scale in either direction.

> Username: mloskot  
> Created_at: 2021-05-30 19:28:10 UTC  
> Updated_at: 2021-05-30 19:28:37 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r642117933  

[Focus on the interface. (...) Use algorithms instead of loops](https://www.youtube.com/watch?v=W2tWOdzgXHA) a golden advice from [Sean Parent](https://www.meetingcpp.com/blog/items/interview-with-sean-parent.html)

> Username: meshtag  
> Created_at: 2021-06-01 19:03:45 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r643407702  

@mloskot, I have refactored the code as per @simmplecoder 's [suggestion](https://github.com/boostorg/gil/pull/612#discussion_r642115682). Can you please have a look at it and state whether this comment is resolvable.

---

📁 test/extension/numeric/convolve.cpp

```diff
 202 |+ };
 203 |+ 
 204 |+ struct test_image_5x5_kernel_1x9_boundary_output_zero
```

> Username: simmplecoder  
> Created_at: 2021-05-30 19:06:28 UTC  
> Updated_at: 2021-05-30 19:13:59 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r642115514  

The test uses 1x3 kernel, am I missing some context on why this is named 1x9?

> Username: meshtag  
> Created_at: 2021-05-31 04:19:38 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r642200090  

You are right, I was using 1x9 kernel in an earlier version.

---

📁 test/extension/numeric/convolve.cpp

```diff
 185 |+             auto img_expected_col_it = gil::view(img_expected_col).col_begin(x);
 186 |+             for (std::ptrdiff_t y = 2; y < img_view.height(); ++y)
 187 |+                 img_expected_col_it[y] = img_expected_row_it[y - 2];
```

> Username: simmplecoder  
> Created_at: 2021-05-30 19:07:53 UTC  
> Updated_at: 2021-05-30 19:13:59 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r642115682  

It seems like the test has quite a bit in common with the previous one (extend zero). Perhaps there are ways to neatly refactor them and modify only the last bit? My comment about copying into vector will not apply then.

> Username: meshtag  
> Created_at: 2021-06-01 15:02:08 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r643189894  

I have refactored the code now, please have a look at it and tell me if I need to change anything.

> Username: simmplecoder  
> Created_at: 2021-06-01 16:34:37 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r643273828  

A bit overengineered, but perhaps we can use it later to write more tests.

---

📁 test/extension/numeric/convolve.cpp

```diff
 147 |+ 
 148 |+         auto const kernel = fixture::create_kernel<channel_t>({0, 0, 0, 0, 0, 0, 1, 0, 0});
 149 |+         gil::detail::convolve_1d<pixel_t>(gil::const_view(img_out), kernel, gil::view(img_out),
```

> Username: simmplecoder  
> Created_at: 2021-05-30 19:13:42 UTC  
> Updated_at: 2021-05-30 19:13:59 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r642116412  

Shouldn't `img_out` be just `img`? I guess it got copypasted everywhere.

> Username: meshtag  
> Created_at: 2021-05-31 04:31:06 UTC  
> Updated_at: 2021-05-31 04:31:07 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r642202971  

Yes you are right, we can change `img_out` to `img` there. I simply chose not to because [code](https://github.com/boostorg/gil/blob/7dd61209a81695600d773f481bb8b22b0f74a30a/test/extension/numeric/convolve.cpp#L102) committed to this file earlier was using it in that manner. However, I have no objections in changing it if we are fine with that change in existing style. Shall I change it then?

> Username: simmplecoder  
> Created_at: 2021-06-01 16:34:17 UTC  
> Url: https://github.com/boostorg/gil/pull/612#discussion_r643273361  

Yes, it is more natural that way and will not confuse anyone.


---

## Comment 3

> Username: simmplecoder  
> Created_at: 2021-06-01 16:42:24 UTC  
> Url: https://github.com/boostorg/gil/pull/612#issuecomment-852275945  

I had a look at the error GA are giving. It seems like the compiler uses C++11 mode but standard library doesn't ... It seems more like C++20 is being used for standard library. I wanted to have a look at the CI script for GA, but couldn't find it. My guess off the bat would be that problem lies in the `CMakeLists.txt` explicitly setting `CMAKE_CXX_STANDARD` and GA passing it some other way which makes build select C++11 compiler mode and C++20 library.

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-06-03 06:51:40 UTC  
> Url: https://github.com/boostorg/gil/pull/612#issuecomment-1145645495  

@meshtag Could you tell what is the status of this PR? Is this going to be picked up or drop it and close it?

---
