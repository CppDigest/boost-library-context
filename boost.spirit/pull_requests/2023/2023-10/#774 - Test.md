# #774 Test [Closed]

> Username: Flamefire  
> Created at: 2023-10-07 16:27:05 UTC  
> Updated at: 2023-10-09 10:50:05 UTC  
> Closed at: 2023-10-09 07:39:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/774  

Combines #766, #767 & #773 to verify CI passes.  
  
Not intended to be merged, rather merge the above PRs.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2023-10-08 09:25:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/774#issuecomment-1751974865  

@Lastique Boost.TypeIndex (transitively included by Boost.Variant) and Boost.Variant have dropped C++03 support, see e.g. https://github.com/boostorg/type_index/commit/b9815d397b079735b8fa1ec96ffedb99b6c553d6 & https://github.com/boostorg/variant/commit/7b45064ed8e3ddb61bc1dfb4f1ba2b5b6b7cb887  
This makes at least the tests incompatible with msvc-10/11/12 and both have stopped running the appveyor test for those. Maybe Spirit should just follow.  
  
Besides this all looks good so the individual PRs should be fine to finally merge!

---

## Comment 2

> Username: djowel  
> Created_at: 2023-10-08 09:41:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/774#issuecomment-1751978072  

> @Lastique Boost.TypeIndex (transitively included by Boost.Variant) and Boost.Variant have dropped C++03 support, see e.g. [boostorg/type_index@b9815d3](https://github.com/boostorg/type_index/commit/b9815d397b079735b8fa1ec96ffedb99b6c553d6) & [boostorg/variant@7b45064](https://github.com/boostorg/variant/commit/7b45064ed8e3ddb61bc1dfb4f1ba2b5b6b7cb887) This makes at least the tests incompatible with msvc-10/11/12 and both have stopped running the appveyor test for those. Maybe Spirit should just follow.  
>   
> Besides this all looks good so the individual PRs should be fine to finally merge!  
  
Agreed. Tell me if it is safe to merge now.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2023-10-08 10:22:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/774#issuecomment-1751986945  

I have created #775 for that, so I think you can merge https://github.com/boostorg/spirit/pull/766, https://github.com/boostorg/spirit/pull/767 & https://github.com/boostorg/spirit/pull/773   
  
I'd suggest merging #775 & #773 first, then either #766 or #767 then rebase the other one to make sure CI now passes.  
  
For your convenience I updated this PR with your changes to #767 and included the appveyor fix here, so if this passes here you can just merge all the above 4 and be sure CI will pass eventually.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2023-10-08 15:43:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/774#issuecomment-1752089716  

@djowel Looks good so https://github.com/boostorg/spirit/pull/775, https://github.com/boostorg/spirit/pull/773, https://github.com/boostorg/spirit/pull/766 & https://github.com/boostorg/spirit/pull/767 can all be merged and this PR can be closed.

---

## Comment 5

> Username: djowel  
> Created_at: 2023-10-08 23:08:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/774#issuecomment-1752187170  

Merged. Feel free to close. Many thanks, everyone!

---

## Comment 6

> Username: djowel  
> Created_at: 2023-10-08 23:26:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/774#issuecomment-1752190955  

Please monitor the tests to ensure everything is fine.

---

## Comment 7

> Username: Flamefire  
> Created_at: 2023-10-09 07:39:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/774#issuecomment-1752487124  

All green :partying_face:

---

## Comment 8

> Username: djowel  
> Created_at: 2023-10-09 08:58:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/774#issuecomment-1752595037  

> All green 🥳  
  
Wonderful!

---

## Comment 9

> Username: Lastique  
> Created_at: 2023-10-09 10:50:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/774#issuecomment-1752777512  

Please, don't forget to merge to master.

---
