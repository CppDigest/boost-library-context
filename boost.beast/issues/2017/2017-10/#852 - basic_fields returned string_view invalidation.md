# #852 - basic_fields returned string_view invalidation [Open]

> Username: vinniefalco  
> Created at: 2017-10-28 23:37:41 UTC  
> Updated at: 2018-12-21 15:08:49 UTC  
> Url: https://github.com/boostorg/beast/issues/852  

The documentation for some basic_fields members should note that certain previously returned string_views are invalidated. For example erasing a field will invalidate any previously returned string views to that field's name or value.
