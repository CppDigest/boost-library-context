# #97 - activity tracker [Open]

> Username: klemens-morgenstern  
> Created at: 2023-08-31 04:35:39 UTC  
> Updated at: 2023-08-31 04:35:39 UTC  
> Url: https://github.com/boostorg/cobalt/issues/97  

Coroutines should have an optoinal activity tracker, similar to asio's handler tracking.  
  
1. Capture source_loc in get_return_object() and then wrap all co_awaits in the await_transform.
