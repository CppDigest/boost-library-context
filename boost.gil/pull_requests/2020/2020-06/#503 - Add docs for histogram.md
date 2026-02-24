# #503 Add docs for histogram [Merged]

> Username: codejaeger  
> Created at: 2020-06-26 21:24:47 UTC  
> Updated at: 2021-03-24 20:19:06 UTC  
> Merged at: 2021-03-24 20:19:06 UTC  
> Closed at: 2021-03-24 20:19:06 UTC  
> Url: https://github.com/boostorg/gil/pull/503  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
Add documentation for the histogram functionality provided in PR #499 .  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
- Blocked by #499  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass - RIP Travis CI, replaced with GitHub Actions  
- [ ] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2020-06-26 23:29:42 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/503#pullrequestreview-438644286  

It looks very good!  
Quick review of the docs structure:  
  
1. No need for `core/` subdirectory, just move the files one level up, and remove the directory. The name core is an informal one, that, there is no physical structure named core.   
  
2. The `extension/' directory does make sense, please keep it.   
  
3. Please, remove `_a_`  infix and `_it` suffix from file names. Simple names like create.rst, fill.rst, subhistogram.rst are clear enough.

---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2020-06-30 22:18:12 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/503#pullrequestreview-440425050  

Nitpick: The file name `cumulativehistogram.rst` should read `cumulative.rst`. It already lives in `histogram/` directory.

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2020-07-26 20:06:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/503#pullrequestreview-455389767  

@codejaeger Well done! It's great to see GSoC features covered with documentation :-) I'm happy to approve this PR. No objections from you @lpranam ?  
  
------  
  
I have offered a few comments, but those are not show-stoppers.  
Technical writing is not a trivial activity. We can always improve and refine documentation in future.  
  
------  
  
I'd suggest we merge it **after** we merge PR #499.  
Next time you may prefer to stick docs in the same PR as implementation and tests.

📁 doc/histogram/create.rst

```diff
  22 |+ **Method 2** - Using make_histogram()
  23 |+ 
  24 |+ There is an alternative way to create the histogram directly from
```

> Username: mloskot  
> Created_at: 2020-07-26 19:35:51 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r460564927  

It would be good to indicate what distinguishes the Method 1 from 2:  
  
```  
- to create the histogram   
+ to create and fill the histogram based on the frequency of luminance in given image  
```

---

📁 doc/histogram/create.rst

```diff
  29 |+     auto h = make_histogram(view(image));
  30 |+ 
  31 |+ where *image* could be a `gray8_image_t`/`rgb8_image_t` object read from source.
```

> Username: mloskot  
> Created_at: 2020-07-26 19:36:35 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r460564964  

Please, use reStructuredText double backticks to indicate inline code or variable names in a sentence   
```  
  
- *image*  
+ ``image``  
```


📁 doc/histogram/cumulative.rst

```diff
   7 |+ --------
   8 |+ 
   9 |+ A cumulative histogram is exactly what it sounds like. The GIL histogram class has the option
```

> Username: mloskot  
> Created_at: 2020-07-26 19:42:30 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r460565543  

Although most people should understand what you mean by  
> A cumulative histogram is exactly what it sounds like.  
I'm not sure if this is a suitable expression.  
  
I'd either remove this sentence and assume a reader is familiar notion of 'cumulative histogram' or provide clear explanation, for example:  
  
>  A cumulative histogram is a histogram where, each bin of given axis stores the counts for that bin plus all bins for smaller values (frequencies) along the given axis.


📁 doc/histogram/extend.rst

```diff
  12 |+ 
  13 |+ In case we need a histogram with an axes of an arbitrary type not identified by
  14 |+ the standard, you need to provide a overload for the hashing function used by the
```

> Username: mloskot  
> Created_at: 2020-07-26 19:43:58 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r460565669  

Nitpicking:  
  
> by the standard  
  
What standard? :-)

---

📁 doc/histogram/extend.rst

```diff
  21 |+ 
  22 |+ Example:
  23 |+ Let's consider you need an histogram with an axis over class Test.
```

> Username: mloskot  
> Created_at: 2020-07-26 19:48:55 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r460566130  

For example, let's consider...

---

📁 doc/histogram/extend.rst

```diff
  33 |+         int a;
  34 |+         Test(): a(0) {}
  35 |+         Test(int c) : a(c) {}
```

> Username: mloskot  
> Created_at: 2020-07-26 19:49:41 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r460566227  

Nitpickign: A more C++11-thy code :)  
  
```cpp  
int a{0};  
Test() = default;  
Test(int c) : a(c) {}  
```

---

📁 doc/histogram/extend.rst

```diff
  49 |+     }
  50 |+ 
  51 |+ Now lets get to the main code.
```

> Username: mloskot  
> Created_at: 2020-07-26 19:50:24 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r460566293  

```  
- to the main code.  
+ to the usage example.  
```


📁 doc/histogram/fill.rst

```diff
  11 |+ --------
  12 |+ 
  13 |+ We will demonstrate the available options for filling and getting in data into the histogram
```

> Username: mloskot  
> Created_at: 2020-07-26 19:52:49 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r460566517  

Perhaps this reads better:  
  
```  
- filling and getting in data into the histogram  
+ filling instance of `histogram` class with values  
```


📁 doc/image_processing/contrast_enhancement/histogram_equalization.rst

```diff
  76 |+     :height: 250px
  77 |+     :alt: Could not load image.
  78 |+     :figclass: align-center
```

> Username: mloskot  
> Created_at: 2020-07-26 20:04:16 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r460567531  

I do not like the idea of storing 7MB files in GIL's repo.  
Unfortunately, I can not offer an ideal alternative yet.  
  
I'd suggest a temporary solution:  
1. Remove the images from this PR  
2. Paste the images as comments to this PR  
3. Replace filename with URLs from GitHub  
  
```  
.. figure:: https://user-images.githubusercontent.com/12345/12811123-3877a980-8375-11ea-9960-1b88a3f9f87b.png  
...  
```  
  
The thing is, once we start adding big binary files to Git repo, then even if we find an optimal solution for hosting images for docs, we will not be able to get rid of them easily, from Git history.  
  
I have (slowly) started collecting externally hosted test images in dedicated repo https://github.com/boost-gil/test-images, so I perhaps we could have `examples/` folder there and link from docs to there.  
  
What do you think @lpranam ?

> Username: lpranam  
> Created_at: 2020-07-28 17:39:53 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r461757419  

In my opinion, in docs we don't have to use the full-size images. Docs just need to demonstrate what our algorithms can do and do not need to show the exact accurate images, we can use highly compressed and small resolution images in docs. Otherwise what @mloskot suggested the great. I have no other objections regarding this PR.

> Username: codejaeger  
> Created_at: 2020-07-31 09:33:11 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r463507846  

> @codejaeger Well done! It's great to see GSoC features covered with documentation :-) I'm happy to approve this PR.  
>   
  
Thanks a lot @mloskot : )    
  
> I'd suggest we merge it **after** we merge PR #499.  
> Next time you may prefer to stick docs in the same PR as implementation and tests.  
  
Yes sure. Also this PR contains docs for the algorithm histogram equalization. I haven't pushed it yet since the histogram class is a dependency for it. Should I remove the docs for the algorithm?

> Username: codejaeger  
> Created_at: 2020-07-31 09:35:15 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r463508794  

I have compressed the images, they now occupy about 34 KB and 24 KB. I can also do what @mloskot suggested if needed.

> Username: lpranam  
> Created_at: 2020-07-31 11:58:34 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r463568444  

though it is compressed for doc would be great to add real one on that repo so we can have those images for later.

> Username: mloskot  
> Created_at: 2020-08-03 17:10:25 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r464546262  

@codejaeger If you want to try the idea outlined earlier, you should have now the write access to https://github.com/boost-gil/test-images Feel free to create `doc/` directory (and any necessary subfolders) and put your images in there.

> Username: mloskot  
> Created_at: 2021-01-24 20:12:22 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r563343287  

@codejaeger Could you add full size/resolution images to the `test-images` repo as @lpranam suggested?

> Username: mloskot  
> Created_at: 2021-03-24 09:38:55 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r600314157  

@codejaeger Ping ☝️

> Username: codejaeger  
> Created_at: 2021-03-24 13:08:50 UTC  
> Updated_at: 2021-03-24 13:36:37 UTC  
> Url: https://github.com/boostorg/gil/pull/503#discussion_r600460176  

Yep doing it. Thanks for the reminder, sorry I had totally forgotten about it.


---

## Comment 4

> Username: mloskot  
> Created_at: 2021-01-24 13:12:54 UTC  
> Url: https://github.com/boostorg/gil/pull/503#issuecomment-766346062  

@codejaeger I've just `git merge`-updated your `docs_for_histogram` branch from the latest `develop`

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2021-03-24 14:32:03 UTC  
> Updated_at: 2021-03-24 15:17:29 UTC  
> Url: https://github.com/boostorg/gil/pull/503#issuecomment-805870689  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/503?src=pr&el=h1) Report  
> Merging [#503](https://codecov.io/gh/boostorg/gil/pull/503?src=pr&el=desc) (b09a0c2) into [develop](https://codecov.io/gh/boostorg/gil/commit/0c0fe1ae79053d8aedfa95eca39329d135e4ea1c?el=desc) (0c0fe1a) will **increase** coverage by `0.78%`.  
> The diff coverage is `98.35%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #503      +/-   ##  
===========================================  
+ Coverage    77.81%   78.59%   +0.78%       
===========================================  
  Files          110      117       +7       
  Lines         4367     5003     +636       
===========================================  
+ Hits          3398     3932     +534       
- Misses         969     1071     +102       
```

---
