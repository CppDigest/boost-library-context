# #1668 - Better control of HTTP fields [Closed]

> Username: brjoha  
> Created at: 2019-07-28 01:43:30 UTC  
> Updated at: 2022-06-17 03:51:04 UTC  
> Closed at: 2022-06-16 16:38:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1668  

I'm pretty sure the answer is no, but thought I'd check in to be sure...  
  
I'm using Beast in an application that also speaks HTTP/2.0, and up until now, I haven't run into any problems coercing Beast into handling the new pseudo headers.  
  
The problem I'm facing is that header order is important in HTTP/2.0 and I need to insert (or update) a header in a specific location.  However, http::basic_fields only has a set() which inserts at the end of the list.  
  
Am I missing something?  The ugly way out of this is to copy the fields, clear, and then copy back with an insert where I need it.  Not ideal :(  
  
I think I also saw somewhere that the fields concept is going away.  When is that expected, and will control over field order be part of the replacement concept?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-07-28 01:53:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1668#issuecomment-515726228  

The _Fields_ concept is likely going to go away but the `basic_fields` class is not - Beast has to provide a container that supports a uniform API for manipulating HTTP fields. That said, Beast **should** provide all of the tools you need to have mostly complete control over the fields. The only caveat is that when there are multiple fields with the same name, Beast will insist that they are sequentially ordered.  
  
The behavior of `set` should be to "update in place" if the name is identical, otherwise insert at the end.  
  
There should be uniform iterators and overloads of `insert` which allow placement.  
  
Finally, it should be possible to extract an element from the container and have it exist outside the container (without allocating memory), and then re-insert the element elsewhere. This is a feature already present in C++20 containers, described in this paper:  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3645.pdf

---

## Comment 2

> Username: brjoha  
> Created at: 2019-08-10 17:05:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1668#issuecomment-520164946  

Just want to follow-up that we have a workaround to replace a header without affecting the header order, in case anyone else runs into the same problem.  
  
We get the iterator of the existing header with find(), then insert() the replacement header using the same name, and finally erase() the old header using its iterator from the earlier find().

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-08-10 17:06:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1668#issuecomment-520165000  

I'll keep this open to track the feature (which I do plan on adding!)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-06-16 16:38:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1668#issuecomment-1157892059  

This new library under development gives better control, and better performance https://github.com/CPPAlliance/http_proto

---

## Comment 5

> Username: brjoha  
> Created at: 2022-06-16 21:26:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1668#issuecomment-1158144219  

Nice.  What's the relationship between Beast and this new library?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-06-16 21:27:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1668#issuecomment-1158146365  

> Nice. What's the relationship between Beast and this new library?  
  
Same author. That's about it.

---

## Comment 7

> Username: brjoha  
> Created at: 2022-06-16 22:05:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1668#issuecomment-1158178523  

Found a post of yours in boost-developers-archive that sheds a little more light.  Vaguely, it's like decoupling the networking from Beast and enhancing the capabilities of what remains.  
  
It's not unlike how this thread started, where I used Beast to work with HTTP messages but handled the I/O over HTTP/2.0 separately.  I just started looking at HTTP/3.0 and was planning to take the same approach.  
  
May make more sense for me to look at doing that with your new lib, and then migrating over the 1.1 and 2.0 flavors afterwards.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-06-17 03:51:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1668#issuecomment-1158462162  

Well for the HTTP header this new library just stores it as a serialized string, instead of a clumsy node-based container that tries to be a budget `unordered_map`. When you append a field it just appends the serialized string and adjusts the final CRLF as needed. You can insert, erase, and move things around with it and it just does whatever necessary `memmove` is needed to perform the operation. There's a small table that stores the offsets so that array indexing is O(1):  
  
https://github.com/CPPAlliance/http_proto/blob/7ff581a3d68d56a02e1936b6a3efc474836d4f56/include/boost/http_proto/fields_base.hpp  
  
It really is much simpler this way and it becomes easier to re-use the buffer on subsequent messages.
