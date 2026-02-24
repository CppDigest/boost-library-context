# #165 Move checked deleters into internal namespace to block unintended ADL [Merged]

> Username: Lastique  
> Created at: 2024-02-07 12:14:55 UTC  
> Updated at: 2024-02-08 15:40:11 UTC  
> Merged at: 2024-02-08 15:39:55 UTC  
> Closed at: 2024-02-08 15:39:55 UTC  
> Url: https://github.com/boostorg/core/pull/165  

This prevents adding namespace boost to ADL when the deleters are used in template parameters, e.g. in `std::unique_ptr`.

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-02-08 11:10:19 UTC  
> Url: https://github.com/boostorg/core/pull/165#issuecomment-1933852864  

@pdimov Any objections?

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-02-08 15:05:00 UTC  
> Url: https://github.com/boostorg/core/pull/165#issuecomment-1934311509  

It will break forward declarations of `checked_deleter`, but those should be rare, if they exist at all. So no.

---
