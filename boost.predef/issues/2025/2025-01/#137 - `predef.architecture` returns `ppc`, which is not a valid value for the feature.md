# #137 - `predef.architecture` returns `ppc`, which is not a valid value for the feature [Closed]

> Username: pdimov  
> Created at: 2025-01-07 15:10:25 UTC  
> Updated at: 2025-01-12 15:58:59 UTC  
> Closed at: 2025-01-12 15:58:59 UTC  
> Url: https://github.com/boostorg/predef/issues/137  

The [rule `architecture` from `tools/check/predef.jam`](https://github.com/boostorg/predef/blob/9aca7f5b609a731106a6d70e8dca9a4196dca968/tools/check/predef.jam#L84) can return `ppc`, which is not a valid value for the `<architecture>` feature. The valid value is `power`.  
  
This breaks Context on ppc64le.
