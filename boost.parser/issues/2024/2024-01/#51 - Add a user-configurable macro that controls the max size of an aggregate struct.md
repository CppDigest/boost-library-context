# #51 - Add a user-configurable macro that controls the max size of an aggregate struct [Closed]

> Username: tzlaine  
> Created at: 2024-01-06 06:35:04 UTC  
> Updated at: 2024-01-07 06:50:38 UTC  
> Closed at: 2024-01-07 06:50:38 UTC  
> Url: https://github.com/boostorg/parser/issues/51  

Currently, the value is 50, but that seems to be affecting compile times somewhat.  It should probably be defaulted to something like 25, and then the user can tweak is as needed.
