# #36 - Inccorect use of rfc2616 in basic_headers [Closed]

> Username: 0x0ACB  
> Created at: 2016-07-04 17:28:25 UTC  
> Updated at: 2016-07-22 15:33:00 UTC  
> Closed at: 2016-07-22 15:32:59 UTC  
> Url: https://github.com/boostorg/beast/issues/36  

Currently basic_headers applies rfc2616 to every header but the rfc states  
  
"Multiple message-header fields with the same field-name MAY be  
present in a message if and only if the entire field-value for that  
header field is defined as a comma-separated list [i.e., #(values)].  
It MUST be possible to combine the multiple header fields into one  
"field-name: field-value" pair, without changing the semantics of the  
message, by appending each subsequent field-value to the first, each  
separated by a comma."  
  
which implies that this is not valid for all headers. As such basic_headers should retain the structure as is.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-07-04 17:45:01 UTC  
> Url: https://github.com/boostorg/beast/issues/36#issuecomment-230333310  

This brings up an interesting question, what should the behavior of `basic_headers::replace` do? There are a few choices:  
1. Replace the first occurrence  
2. Replace the first occurrence, remove any additional occurrences  
3. Replace the first occurrence if there is only one occurrence, else throw an exception.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-07-04 17:49:08 UTC  
> Url: https://github.com/boostorg/beast/issues/36#issuecomment-230333749  

Another problem, what happens with `basic_headers::operator[]` if there is more than one field with the same name? I guess it could return the first occurrence.

---

## Comment 3

> Username: 0x0ACB  
> Created at: 2016-07-04 17:53:10 UTC  
> Url: https://github.com/boostorg/beast/issues/36#issuecomment-230334171  

i would remove operator[] and instead do something like equal_range in multimap or maybe return a vector with all values.   
  
i would say replace should do remove(name) insert(name, value)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-07-04 17:57:23 UTC  
> Url: https://github.com/boostorg/beast/issues/36#issuecomment-230334625  

The problem with removing `operator[]` is that it inconveniences the majority of use-cases where only one field with a given name exists. For example:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/impl/stream.ipp#L970  
  
Also note that the fields in the headers are in insertion order and not in set order. This is an important detail, because proxies are not supposed to reorder header fields. Many other HTTP libraries make the mistake of returning the fields in alphabetical order, which breaks proxies.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-07-04 18:00:16 UTC  
> Url: https://github.com/boostorg/beast/issues/36#issuecomment-230334920  

This seems to pass tests, it changes `basic_headers` to behave like a `std::multiset`  
https://github.com/vinniefalco/Beast/commits/rfc

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-07-22 15:32:59 UTC  
> Url: https://github.com/boostorg/beast/issues/36#issuecomment-234576381  

Fixed in _1.0.0-b8_
