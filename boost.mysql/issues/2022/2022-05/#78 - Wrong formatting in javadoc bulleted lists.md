# #78 - Wrong formatting in javadoc bulleted lists [Closed]

> Username: vinniefalco  
> Created at: 2022-05-14 15:09:54 UTC  
> Updated at: 2023-02-24 19:20:42 UTC  
> Closed at: 2023-02-24 19:20:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/78  

The bulleted list here is not formatted correctly:  
https://anarthal.github.io/mysql/mysql/ref/boost__mysql__row.html  
  
It should use `@li` for each list item, like this:  
https://github.com/boostorg/beast/blob/a593738e983514a14e1a8f9994b9a08cf809ca40/include/boost/beast/websocket/stream.hpp#L109
