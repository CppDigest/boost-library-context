# #844 - `boost::json::serializer` should take a `storage_ptr` [Closed]

> Username: pdimov  
> Created at: 2023-01-20 02:31:02 UTC  
> Updated at: 2023-01-28 11:04:18 UTC  
> Closed at: 2023-01-28 11:04:18 UTC  
> Url: https://github.com/boostorg/json/issues/844  

When converting a JSON value to a string, it's sometimes necessary to use a specific `storage_ptr` for the temporary allocations done by the `json::serializer`. One such example in https://github.com/pdimov/mustache/blob/develop/src/renderer.cpp#L849. The Mustache library does all its temporary allocations with a user-provided `storage_ptr`, so it would be nice if I can give the `serializer` that pointer.
