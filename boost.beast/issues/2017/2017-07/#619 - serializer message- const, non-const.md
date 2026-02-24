# #619 - serializer message: const, non-const [Closed]

> Username: vinniefalco  
> Created at: 2017-07-07 17:02:30 UTC  
> Updated at: 2017-07-09 01:53:43 UTC  
> Closed at: 2017-07-09 01:53:43 UTC  
> Url: https://github.com/boostorg/beast/issues/619  

`serializer` constructor should take const or non const message depending on if the `Body::reader` needs a const or non const reference to construct. This lets a read-stateful body value_type work.
