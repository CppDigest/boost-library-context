# #1681 - http::parser should be reusable [Open]

> Username: vinniefalco  
> Created at: 2019-08-13 14:38:16 UTC  
> Updated at: 2019-08-13 14:55:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1681  

It should not be necessary to construct a new parser for each message. However this raises design questions. What if the contained message is not movable or requires parameters to construct? A solution is to support move-assignment, and allow move-construction if the message is move constructible. However, the Writer has to also be movable and that's a problem for some body types.
