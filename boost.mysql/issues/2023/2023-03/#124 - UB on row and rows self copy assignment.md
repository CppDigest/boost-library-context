# #124 - UB on row and rows self copy assignment [Closed]

> Username: anarthal  
> Created at: 2023-03-02 15:37:28 UTC  
> Updated at: 2023-03-02 20:58:02 UTC  
> Closed at: 2023-03-02 20:58:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/124  

The self-assignment protection we have for view assignment is not in place for copy assignment. This causes memcpy's with overlapping addresses.
