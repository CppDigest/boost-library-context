# #16 - parser class bypasses storage class on finish() and write() [Closed]

> Username: edtanous  
> Created at: 2019-12-03 03:01:22 UTC  
> Updated at: 2019-12-03 04:32:54 UTC  
> Closed at: 2019-12-03 04:00:36 UTC  
> Url: https://github.com/boostorg/json/issues/16  

When a parser object is provided storage during the call to start(), subsequent calls to finish will bypass the storage object, and call operator new directly.  It's not clear if this is intended, or an oversight.  Digging into the code, it looks like the parser object holds a raw_stack object, which contains its own storage_ptr.  Unfortunately (or intentionally), the parser constructor doesn't have an constructor that can take a reference to a storage_ptr, so raw_stack gets default initialized to a default_impl backed storage class in all cases, and bypasses the storage class entirely for the lifetime of the parser.  
  
```  
  boost::json::storage_ptr storage =  
      boost::json::make_storage<boost::json::pool>(1024 * 1024);  
  boost::json::parser p;  
  p.start(storage);  
  const std::array<char, 2> input = {'{', '}'};  
  p.finish(input.data(), input.size());  // This calls operator new through boost::json::detail::default_impl::allocate  
```  
  
From the storage docs, this seems like unintended behavior, and one would expect the provided storage class to be used for memory during the parse operation.  
  
placing this code:  
```  
 rs_ = detail::raw_stack(sp);  
```  
On the line below causes start to reconstruct the raw_stack object with the new storage, and causes the behavior I would expect, but it's not clear that's the intent, and might be inefficient to reconstruct the stack object if the same storage is reused repeatedly.  
https://github.com/vinniefalco/json/blob/c08721906d67c17eb8bda679550ca7710856d917/include/boost/json/impl/parser.ipp#L165

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-03 03:13:35 UTC  
> Updated at: 2019-12-03 03:14:22 UTC  
> Url: https://github.com/boostorg/json/issues/16#issuecomment-560979229  

> It's not clear if this is intended, or an oversight.  
  
It is intended for now. The storage provided in the call to `start()` is the one that is used for building the final `json::value`. The internal stack uses the global operator new/delete as you have observed. We really don't want the stack to use the same storage as the final `value`, because consider what happens if you use a `pool` - it will end up way over-allocating in the pool, and then when the parsing is complete there's no way to re-use the stack.  
  
Some of the design and implementation decisions I made are slightly biased towards networking use-cases, such as usage with Asio, Beast, and other new network libraries and programs which I plan on releasing soon. You store a `parser` in each `connection` object, and the stack is re-used for each I/O cycle for the lifetime of the connection.  
  
If we want to give callers separate control over the stack's allocator (which is probably a good idea), the way to do it is to pass it as a constructor argument. However, the current `storage` API is not sufficient. There needs to be a way for the caller to provide their own memory buffer which is used first. Since this memory buffer is sized by the caller and not by the parser, additional signatures are needed in `storage`.  
  
I decided to delay the design work needed to realize this feature, to keep the current incarnation of the library simple and have the opportunity for feedback on the larger pieces. But I'm more than happy to explore this with you. Can you describe your use-case or needs?  
  
Thanks

---

## Comment 2

> Username: edtanous  
> Created at: 2019-12-03 03:23:45 UTC  
> Url: https://github.com/boostorg/json/issues/16#issuecomment-560981249  

Thanks for the brain dump.  The use case was largely the same as yours, storing a parser in each "connection" object along with some pre-allocated/reserved storage to avoid inadvertent calls to malloc during the handling of incoming data.  
  
With the existing apis, I suspect I can "warm" the parser/stack by simply parsing a relatively large/complex object then clearing it before construction.  
  
Will close this for now, as I'm still just poking around, and I don't understand things well enough yet to propose a better API, although I came to the same conclusion.  
  
Thanks for the great library.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-12-03 03:26:45 UTC  
> Url: https://github.com/boostorg/json/issues/16#issuecomment-560981819  

> With the existing apis, I suspect I can "warm" the parser/stack by simply parsing a relatively large/complex object then clearing it before construction.  
  
We could add `void parser::reserve( std::size_t bytes )` or something like that

---

## Comment 4

> Username: edtanous  
> Created at: 2019-12-03 03:29:04 UTC  
> Url: https://github.com/boostorg/json/issues/16#issuecomment-560982300  

That API would simplify things considerably.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-12-03 03:30:15 UTC  
> Url: https://github.com/boostorg/json/issues/16#issuecomment-560982549  

How would you estimate "bytes" ? Maybe it should be measured in the number of `value` objects you expect to have in a typical JSON? it could be `std::size_t count` instead

---

## Comment 6

> Username: edtanous  
> Created at: 2019-12-03 03:38:25 UTC  
> Url: https://github.com/boostorg/json/issues/16#issuecomment-560984228  

> How would you estimate "bytes" ? Maybe it should be measured in the number of `value` objects you expect to have in a typical JSON? it could be `std::size_t count` instead  
  
I'm a little lost at how a count API would work, given that json objects aren't a fixed total size, given that strings can be any length.  In my case, I have a relatively low number of connection objects, and relatively simple/small json to parse, so reserving them to inordinately large buffers upfront would've worked for me.  Ideally I was looking to define a templated static fixed size storage class (similar to beast flat_static_buffer) that could throw/return nullptr if the available space was exceeded during parsing.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-12-03 03:56:30 UTC  
> Url: https://github.com/boostorg/json/issues/16#issuecomment-560987447  

> I'm a little lost at how a count API would work, given that json objects aren't a fixed total size, given that strings can be any length. I  
  
There is only at most two strings on the stack: one for the key (if parsing an object) and one for a string value. They are immediately removed afterwards, thus the stack really only ever holds a series of `json::value`, and these are all the same size.  
  
> Ideally I was looking to define a templated static fixed size storage class  
  
Yeah this is exactly what I was saying about "caller to provide their own memory buffer which is used first" which is the like the flat static buffer you described.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-12-03 04:00:44 UTC  
> Url: https://github.com/boostorg/json/issues/16#issuecomment-560988135  

I decided to go with `bytes`, I have pushed an update to the _develop_ branch.

---

## Comment 9

> Username: edtanous  
> Created at: 2019-12-03 04:32:54 UTC  
> Url: https://github.com/boostorg/json/issues/16#issuecomment-560993888  

I pulled develop, and calling the reserve() api seems to have solved my problem.  
  
Thanks for the ridiculously quick turnaround.
