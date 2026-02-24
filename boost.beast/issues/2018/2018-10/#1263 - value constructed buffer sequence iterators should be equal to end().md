# #1263 - value constructed buffer sequence iterators should be equal to end() [Closed]

> Username: vinniefalco  
> Created at: 2018-10-07 15:11:13 UTC  
> Updated at: 2018-11-02 15:08:13 UTC  
> Closed at: 2018-11-02 15:08:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1263  

Value constructed (i.e. default constructed) buffer sequence iterators should be equal to `end()` of the underlying container. This is a Ranges requirement.
