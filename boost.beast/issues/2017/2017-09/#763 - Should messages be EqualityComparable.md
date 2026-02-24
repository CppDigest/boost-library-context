# #763 - Should messages be EqualityComparable? [Closed]

> Username: djarek  
> Created at: 2017-09-02 16:07:00 UTC  
> Updated at: 2017-09-02 18:48:54 UTC  
> Closed at: 2017-09-02 18:48:54 UTC  
> Url: https://github.com/boostorg/beast/issues/763  

While writing unit tests I ran into a quality of life issue - messages are not comparable:  
```  
boost::beast::http::request<boost::beast::http::string_body> sent_request, received_request;  
// roundtrip through a custom stream  
BOOST_CHECK_EQUAL(sent_request, received_request); // does not compile  
```  
Is it intended that they are not comparable?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-02 18:35:01 UTC  
> Url: https://github.com/boostorg/beast/issues/763#issuecomment-326762209  

Equality comparison opens up a huge can of worms. What if the fields are in different order? What if the field names have a different capitalization? For testing what I do is just convert them to string (e.g. `boost::lexical_cast<std::string>(m)` and compare that way.

---

## Comment 2

> Username: djarek  
> Created at: 2017-09-02 18:48:39 UTC  
> Url: https://github.com/boostorg/beast/issues/763#issuecomment-326762852  

Okay, haven't thought about that. I guess converting the message into a string should be good enough.
