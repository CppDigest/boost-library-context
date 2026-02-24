# #457 - Integration with <ranges> [Open]

> Username: alandefreitas  
> Created at: 2022-08-25 21:58:35 UTC  
> Updated at: 2022-09-27 20:44:55 UTC  
> Url: https://github.com/boostorg/url/issues/457  

The lazy containers could integrate with std::ranges. For instance, we could `enable_view<>` for them.  
  
https://en.cppreference.com/w/cpp/ranges/view

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-25 21:59:07 UTC  
> Url: https://github.com/boostorg/url/issues/457#issuecomment-1227801887  

segments would probably be more useful than params
