# #80 - Better control over request and response objects with websockets [Closed]

> Username: miguelportilla  
> Created at: 2016-09-16 19:56:08 UTC  
> Updated at: 2017-05-08 00:03:59 UTC  
> Closed at: 2017-05-08 00:03:59 UTC  
> Url: https://github.com/boostorg/beast/issues/80  

It would be nice to have better control over request and response objects with websockets.  Having access to the entire HTTP response object could be very useful. For instance, a unit test may want to inspect the response object after a failed handshake. As a client, you should be able to fully customize a request. For example by providing a user name and password for authentication if needed.
