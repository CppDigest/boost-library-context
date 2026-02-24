# #573 Bring numeric extension to core GIL [Merged]

> Username: lpranam  
> Created at: 2021-03-02 19:28:23 UTC  
> Updated at: 2022-02-18 09:20:09 UTC  
> Merged at: 2022-02-18 08:55:59 UTC  
> Closed at: 2022-02-18 08:55:59 UTC  
> Url: https://github.com/boostorg/gil/pull/573  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
As now we are including new image processing algorithms in GIL numeric extension is very useful and that is why it is being moved into the GIL core  
  
### References  
  
closes #497  
  
### Tasklist  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2021-03-02 20:26:46 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/573#pullrequestreview-602224676  

Good effort on the tedious stuff!  
  
A few comments to consider.

📁 example/harris.cpp

```diff
   9 | #include <boost/gil/extension/io/png.hpp>
   9 |- #include <boost/gil/extension/numeric/convolve.hpp>
  10 |+ #include <boost/gil/image_processing/convolve.hpp>
```

> Username: mloskot  
> Created_at: 2021-03-02 20:15:18 UTC  
> Updated_at: 2021-04-10 14:36:08 UTC  
> Url: https://github.com/boostorg/gil/pull/573#discussion_r585877948  

I guess this was "find & replace" change, but it would be good to sort the includes though.  
  
e.g. here move `convolve.hpp` below `image.hpp`

> Username: lpranam  
> Created_at: 2021-03-03 10:30:15 UTC  
> Updated_at: 2021-04-10 14:36:08 UTC  
> Url: https://github.com/boostorg/gil/pull/573#discussion_r586299233  

Thanks for the reminder. in this case, I can reformat the entire files to clang-format when moving around :D

> Username: mloskot  
> Created_at: 2021-03-03 12:17:09 UTC  
> Updated_at: 2021-04-10 14:36:08 UTC  
> Url: https://github.com/boostorg/gil/pull/573#discussion_r586369028  

Sure


📁 include/boost/gil/algorithm.hpp

```diff
1189 |+ /// The reference proxy is the reference type, but with stripped-out C++ reference.
1190 |+ /// Models PixelConcept.
1191 |+ template <typename T>
```

> Username: mloskot  
> Created_at: 2021-03-02 20:24:31 UTC  
> Updated_at: 2021-04-10 14:36:08 UTC  
> Url: https://github.com/boostorg/gil/pull/573#discussion_r585883486  

The whole core `algorithm.hpp` is becoming huge!  
  
What about that we start splitting it:  
1. `gil/algorithm.hpp` to `gil/algorithm/core.hpp`  
2. `gil/extension/numeric/algorithm.hpp` to `gil/algorithm/numeric.hpp`  
  
Next, we could start splitting `gil/algorithm/core.hpp` into better structured/categorised headers

> Username: lpranam  
> Created_at: 2021-12-30 02:03:01 UTC  
> Url: https://github.com/boostorg/gil/pull/573#discussion_r776545142  

Yes, that makes sense and we should definitely do that. but on the other hand it also gets confusing to me(as a user) from header names it is not clear what will be in what. and then also we have image processing ALGORITHMS which will not be in the algorithm directory. so I am not sure how to proceed with this. or maybe another major reflector and rearranging of algorithms?

> Username: mloskot  
> Created_at: 2022-01-03 17:26:13 UTC  
> Updated_at: 2022-01-03 17:26:47 UTC  
> Url: https://github.com/boostorg/gil/pull/573#discussion_r777608415  

Rearranging of algorithms is something on its way too, I think.  
That is why it would be nice to prepare a clear structure for it.  
  
My idea was to use directory and file names as from general to specific.  
There obviously are at least two approaches/directions:  
  
1.  `gil/algorithm/<what kind of algorithms>.hpp` or `gil/algorithm/<name of algorithm>.hpp`  with `gil/algorithm/core.hpp` are general purpose/basic algorithms.  
2.  `gil/image_processing/algorithm.hpp`, `gil/rasterisation/algorithm.hpp`, `gil/numeric/algorithm.hpp`,  etc.  
  
To be honest, I'm more in favour of the option 1. as it would lead us to filesystem with clear separation of data and algorithms.  
  
> we have image processing ALGORITHMS which will not be in the algorithm directory  
  
Good point. IMO, IP algorithms could be moved too.  
TBH,  `image_processing` as folder name is an unfortunate one (blame on me, I think) as the whole library is about image processing, isn't it :-) So, better, more specific names FTW!

> Username: lpranam  
> Created_at: 2022-01-03 21:30:48 UTC  
> Url: https://github.com/boostorg/gil/pull/573#discussion_r777713780  

agreed. though I would like to keep that in a separate PR/issue and get done with this PR.   
  
ideal steps to move forward IMO:  
1. get this PR done  
2. we refactor the codebase (doing refactor while large PRs are waiting is painful so before or after large PRs)  
3. I start rearranging the algorithms

> Username: mloskot  
> Created_at: 2022-01-03 22:03:39 UTC  
> Url: https://github.com/boostorg/gil/pull/573#discussion_r777726109  

Sure, I'm just brainstorming. This is your PR, so you're in judge of its scope.

> Username: lpranam  
> Created_at: 2022-01-03 22:42:06 UTC  
> Url: https://github.com/boostorg/gil/pull/573#discussion_r777738827  

reviews appreciated :)


📁 include/boost/gil/image.hpp

```diff
  40 | 
  40 |- template< typename Pixel, bool IsPlanar = false, typename Alloc=std::allocator<unsigned char> >
  41 |+ template< typename Pixel, bool IsPlanar, typename Alloc>
```

> Username: mloskot  
> Created_at: 2021-03-02 20:25:55 UTC  
> Updated_at: 2021-04-10 14:36:08 UTC  
> Url: https://github.com/boostorg/gil/pull/573#discussion_r585884444  

This this seems like opposite of the change in the `include/boost/gil/metafunctions.hpp`:  
  
```  
- template <typename Pixel, bool IsPlanar, typename Alloc> class image;  
+ template <typename Pixel, bool IsPlanar = false, typename Alloc=std::allocator<unsigned char> > class image;  
```  
  
Intentional?

> Username: lpranam  
> Created_at: 2021-03-03 10:31:42 UTC  
> Updated_at: 2021-04-10 14:36:08 UTC  
> Url: https://github.com/boostorg/gil/pull/573#discussion_r586300116  

yes, the build was failing after moving things, so I was forced to do this.


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-04-10 14:06:38 UTC  
> Updated_at: 2021-12-30 04:47:57 UTC  
> Url: https://github.com/boostorg/gil/pull/573#issuecomment-817142121  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/573?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#573](https://codecov.io/gh/boostorg/gil/pull/573?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6dfb4ef) into [develop](https://codecov.io/gh/boostorg/gil/commit/4531c3d563cfade3eafb80a09f304747845bbe8b?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4531c3d) will **increase** coverage by `0.00%`.  
> The diff coverage is `86.63%`.  
  
> :exclamation: Current head 6dfb4ef differs from pull request most recent head ebaf247. Consider uploading reports for the commit ebaf247 to get more accurate results  
```diff  
@@           Coverage Diff            @@  
##           develop     #573   +/-   ##  
========================================  
  Coverage    78.71%   78.72%             
========================================  
  Files          118      117    -1       
  Lines         5032     5034    +2       
========================================  
+ Hits          3961     3963    +2       
  Misses        1071     1071             
```

---

## Comment 3

> Username: lpranam  
> Created_at: 2021-07-08 10:01:40 UTC  
> Url: https://github.com/boostorg/gil/pull/573#issuecomment-876306277  

I wish to merge this before we reformat the code base, I'll create another PR for the remaining porting.

---

## Comment 4

> Username: mloskot  
> Created_at: 2021-07-08 11:02:53 UTC  
> Url: https://github.com/boostorg/gil/pull/573#issuecomment-876344406  

@lpranam Sure. Please, proceed as you need. Thanks

---

## Comment 5

> Username: simmplecoder  
> Created_at: 2021-07-10 10:08:16 UTC  
> Url: https://github.com/boostorg/gil/pull/573#issuecomment-877611154  

@lpranam alright. I believe @meshtag can reformat later anyway (it is not his main concern at the moment).

---

## Comment 6

> Username: lpranam  
> Created_at: 2022-01-03 17:16:03 UTC  
> Updated_at: 2022-02-17 10:46:19 UTC  
> Url: https://github.com/boostorg/gil/pull/573#issuecomment-1004235690  

I have moved all the required numeric parts to the core and this PR is now ready for a complete review. :)

---
