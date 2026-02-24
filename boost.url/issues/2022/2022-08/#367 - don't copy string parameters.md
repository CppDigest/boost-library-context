# #367 - don't copy string parameters [Closed]

> Username: vinniefalco  
> Created at: 2022-08-08 00:09:30 UTC  
> Updated at: 2022-08-23 22:30:25 UTC  
> Closed at: 2022-08-23 22:30:02 UTC  
> Url: https://github.com/boostorg/url/issues/367  

With a bunch of refactoring, we can probably avoid the need for `copied_strings`. The obstacle will be the `any_path_iter` and `any_query_iter` implementations which will need the most work, as they have to be re-expressed in terms of a `string_view&` instead of pointers and offsets.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-08 00:10:28 UTC  
> Updated at: 2022-08-08 00:10:35 UTC  
> Url: https://github.com/boostorg/url/issues/367#issuecomment-1207515937  

There are some half-baked ideas in my **op** branch: https://github.com/vinniefalco/url/commit/562b9663aa8c3023ce542a906f3ad5288a969425

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-23 22:30:02 UTC  
> Updated at: 2022-08-23 22:30:25 UTC  
> Url: https://github.com/boostorg/url/issues/367#issuecomment-1224954744  

duplicate of https://github.com/CPPAlliance/url/issues/147
