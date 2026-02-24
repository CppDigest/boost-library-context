# #506 Implement hstack and vstack [Merged]

> Username: simmplecoder  
> Created at: 2020-07-12 20:01:05 UTC  
> Updated at: 2021-01-24 20:43:07 UTC  
> Merged at: 2021-01-24 20:43:06 UTC  
> Closed at: 2021-01-24 20:43:06 UTC  
> Url: https://github.com/boostorg/gil/pull/506  

### Description  
  
Adds ability to stack images either horizontally or vertically. Provides output view argument form and image returning form for convenience. Does full input checking. This is rough version only, not intended for usage outside writing examples.  
  
### References  
  
https://cpplang.slack.com/archives/CSVT0STV2/p1594414134118400  
  
### Tasklist  
  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Commented]

> Username: codejaeger  
> Created_at: 2020-07-14 05:51:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/506#pullrequestreview-447801005  

@simmplecoder I just made a few tiny comments. Apart from that, for the purpose of stacking image views it looks fine.

📁 example/hvstack.hpp

```diff
  17 |+     for (const auto& view : views)
  18 |+     {
  19 |+         if (view.dimensions() != dimensions)
```

> Username: codejaeger  
> Created_at: 2020-07-14 05:39:38 UTC  
> Updated_at: 2020-08-24 18:30:06 UTC  
> Url: https://github.com/boostorg/gil/pull/506#discussion_r454113697  

Maybe we only need to check one dimension. That is, the heights should be same for h-stacking. One advantage could be making a panaroma view.

> Username: simmplecoder  
> Created_at: 2020-08-24 18:30:47 UTC  
> Url: https://github.com/boostorg/gil/pull/506#discussion_r475814032  

I implemented this feature.

---

📁 example/hvstack.hpp

```diff
  61 |+     for (const auto& view : views)
  62 |+     {
  63 |+         if (view.dimensions() != dimensions)
```

> Username: codejaeger  
> Created_at: 2020-07-14 05:43:59 UTC  
> Updated_at: 2020-08-24 18:30:06 UTC  
> Url: https://github.com/boostorg/gil/pull/506#discussion_r454115074  

Maybe we only need to check one dimension. That is, only the widths should be same for v-stacking.

---

📁 example/hvstack.hpp

```diff
  45 |+ 
  46 |+     auto dimensions = views.front().dimensions();
  47 |+     image<typename View::value_type> result_image(dimensions.x * views.size(), dimensions.y);
```

> Username: codejaeger  
> Created_at: 2020-07-14 05:49:35 UTC  
> Updated_at: 2020-08-24 18:30:06 UTC  
> Url: https://github.com/boostorg/gil/pull/506#discussion_r454116954  

Also probably not a required feature, but adding automatic padding to fit in the images could be a good idea. For example, relax the constraint of equal heights on the input images for vstack.

> Username: simmplecoder  
> Created_at: 2020-08-24 18:32:34 UTC  
> Url: https://github.com/boostorg/gil/pull/506#discussion_r475814984  

I believe this is a bit more questionable compared to previous feature. Padding might make it hard to understand which images are padded which ones are not. Since users can pad themselves, I believe making it more explicit is the right way to go. If you have any more arguments in favor of the feature, please unresolve the conversation.

> Username: codejaeger  
> Created_at: 2020-08-24 19:49:22 UTC  
> Url: https://github.com/boostorg/gil/pull/506#discussion_r475854663  

@simmplecoder Yes, I get your argument.  
My point was a simple one, if one provides image1 and image2 we could make a frame of dimension [max(image1.height(), image2.height()), max(image1.width(), image2.width())] for each of the images and stack these frames together.   
GIL already has code to enable padding along rows and columns which could come useful.  
Anyways as I said it is not really a required feature, but just increases the flexibility of the stacking tool.

> Username: simmplecoder  
> Created_at: 2020-08-24 20:14:07 UTC  
> Updated_at: 2020-08-24 20:14:08 UTC  
> Url: https://github.com/boostorg/gil/pull/506#discussion_r475867244  

@mloskot @lpranam what do you think? If the feature is not important from your point of view, the PR is ready for review.

> Username: mloskot  
> Created_at: 2020-08-24 20:19:59 UTC  
> Url: https://github.com/boostorg/gil/pull/506#discussion_r475870219  

> GIL already has code to enable padding along rows and columns which could come useful.  
  
True.  
  
>  Since users can pad themselves, I believe making it more explicit is the right way to go.  
  
Indeed. User has tools to compose desired processing chain.  
  
Here, especially that this is still an example, I'd stick with the simplicity instead of offering rich set of options.


---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2020-08-24 12:57:16 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/506#pullrequestreview-473438577  

~~We should cover all new functions/functionality with tests~~  
I just realised these are examples!  
  
-----  
  
Is this PR related to any of the GSoC 2020 work?

---

## Comment 3

> Username: simmplecoder  
> Created_at: 2020-08-24 13:17:45 UTC  
> Url: https://github.com/boostorg/gil/pull/506#issuecomment-679120556  

@mloskot it is a convenience tool to make putting input and result in perspective easier. It could use a lot of work, but I thought we could do by it for GSoC. Anyway, this is not really needed, but would be a good addition until a better way is found.

---

## Comment 4

> Username: mloskot  
> Created_at: 2020-08-24 13:26:32 UTC  
> Url: https://github.com/boostorg/gil/pull/506#issuecomment-679125156  

@simmplecoder Sounds good. Let's consider it as part of GSoC as part of the whole toolkit.  
  
Could you address @codejaeger 's suggestions?  
If this is something to consider for future improvements, then it may be worth to add them as `// TODO:` comments.

---

## Comment 5

> Username: simmplecoder  
> Created_at: 2020-08-24 13:35:18 UTC  
> Url: https://github.com/boostorg/gil/pull/506#issuecomment-679129874  

@mloskot , sure, will ping you back when ready.

---

## Review 6 [Approved]

> Username: mloskot  
> Created_at: 2021-01-24 20:42:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/506#pullrequestreview-574967141  

LGTM

---
