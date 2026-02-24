# #1677 - Document HTTP free-function return values [Closed]

> Username: mika-fischer  
> Created at: 2019-08-12 16:34:23 UTC  
> Updated at: 2024-02-12 09:16:34 UTC  
> Closed at: 2024-02-12 09:16:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1677  

Is there a possibility to get the number of bytes parsed / serialized in the callbacks to async_read_some / async_write_some? The info must be present, but I can't see a way to access it. But I'm probably missing something...

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-08-12 16:42:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1677#issuecomment-520501899  

Which functions are you talking about? HTTP? Websocket?

---

## Comment 2

> Username: mika-fischer  
> Created at: 2019-08-12 16:43:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1677#issuecomment-520502250  

Sorry, I meant the HTTP ones:  
https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_write_some.html and https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_some.html

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-08-12 16:51:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1677#issuecomment-520505267  

You have uncovered a bug in the documentation, which I am fixing in version 266. If you look at the function signatures in the header, note they return a `std::size_t` indicating the number of bytes transferred to or from the stream:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/read.hpp#L255  
  
I will adjust this issue to reflect the need to update the docs

---

## Comment 4

> Username: mika-fischer  
> Created at: 2019-08-13 08:53:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1677#issuecomment-520749070  

I am aware of the return value, but I was under the impression, that it does not (necessarily) correspond to the number of bytes of the body that were consumed but the number of bytes sent over the stream (which might be more because of chunk encoding, etc.). Is that wrong?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-08-13 13:23:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1677#issuecomment-520832667  

>  it does not (necessarily) correspond to the number of bytes of the body that were consumed but the number of bytes sent over the stream   
  
Ah yes that's correct. Maybe these functions could help?  
  
https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/ref/boost__beast__http__parser/content_length.html  
https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/ref/boost__beast__http__parser/content_length_remaining.html

---

## Comment 6

> Username: mika-fischer  
> Created at: 2019-08-13 14:34:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1677#issuecomment-520861130  

Only partly. They are only available for the parser, not the serializer. And they won't return a value if the content_length is not known a priori.  
  
The last point is probably OK, in that the parser/serializer don't need to keep count if they have no use for this information.  
  
But it would be really nice if there was an easy way to get the number of body bytes sent/received in async_read_some/async_write_some, for instance for the purposes of upload/download progress reports.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-08-13 14:52:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1677#issuecomment-520868767  

`counted_stream` is a general stream layer which will count bytes for you:  
https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/using_io/layered_streams/counted_stream_example_new.html

---

## Comment 8

> Username: mika-fischer  
> Created at: 2019-08-13 15:11:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1677#issuecomment-520877118  

Nice, but again, I'm not interested in the amount of bytes transferred. I get this info already in the callbacks to async_read_some / async_write_some. I'm interested in how many bytes of the body have been read / written in that call to async_read_some / async_write_some and I don't think I get that with counted_stream in the presence of chunked encoding etc.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-08-13 15:19:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1677#issuecomment-520880599  

Yes I see what you mean. If you open a new issue I will mark it as a feature request and get it into the next release.

---

## Comment 10

> Username: ashtum  
> Created at: 2024-02-12 09:16:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1677#issuecomment-1938290200  

Addressed in https://github.com/boostorg/beast/commit/105466172304ee27f6201725fbaa78e478008415.
