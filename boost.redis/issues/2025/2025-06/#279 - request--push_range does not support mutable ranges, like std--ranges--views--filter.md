# #279 - request::push_range does not support mutable ranges, like std::ranges::views::filter [Open]

> Username: anarthal  
> Created at: 2025-06-29 20:45:20 UTC  
> Updated at: 2025-06-29 20:45:20 UTC  
> Url: https://github.com/boostorg/redis/issues/279  

Because the range is taken as `const Range&` rather than `Range&&`
