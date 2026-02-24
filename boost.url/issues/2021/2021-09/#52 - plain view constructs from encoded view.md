# #52 - plain view constructs from encoded view [Closed]

> Username: vinniefalco  
> Created at: 2021-09-27 05:17:43 UTC  
> Updated at: 2021-10-06 17:59:00 UTC  
> Closed at: 2021-10-06 17:59:00 UTC  
> Url: https://github.com/boostorg/url/issues/52  

Statements like this should work:  
```  
segments_view sv = parse_path( "/path/to/file.txt" );  
```  
  
Plus a way to set the alllocator, perhaps allowing copy assignment from `segments_encoded_view`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-10-03 21:35:08 UTC  
> Url: https://github.com/boostorg/url/issues/52#issuecomment-933029308  

The interface is `decoded( allocator )` as a member of the view
