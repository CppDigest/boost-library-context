# #138 Add ICU libs search paths even when ICU_ICUUC_NAME, ICU_ICUDT_NAME, and ICU_ICUIN_NAME are provided by user [Merged]

> Username: volo-zyko  
> Created at: 2021-06-09 18:56:58 UTC  
> Updated at: 2021-06-12 13:22:02 UTC  
> Merged at: 2021-06-12 13:22:02 UTC  
> Closed at: 2021-06-12 13:22:02 UTC  
> Url: https://github.com/boostorg/regex/pull/138  

Since `ICU_ICUUC_NAME`, `ICU_ICUDT_NAME`, and `ICU_ICUIN_NAME` are just names (i.e. not paths to those libs) `ICU_PATH` is still needed to correctly link with ICU.

---
