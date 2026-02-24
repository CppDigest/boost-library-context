# #497 - Change Body::size parameter [Closed]

> Username: vinniefalco  
> Created at: 2017-06-16 00:16:45 UTC  
> Updated at: 2017-06-16 16:19:20 UTC  
> Closed at: 2017-06-16 16:19:20 UTC  
> Url: https://github.com/boostorg/beast/issues/497  

Right now it requires a message but then how do you form a response with an empty body? It should take the `value_type`. But this needs a little thought.
