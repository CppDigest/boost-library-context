# #392 - Banish std::string from basic_fields [Closed]

> Username: vinniefalco  
> Created at: 2017-05-27 21:23:18 UTC  
> Updated at: 2017-06-08 03:14:43 UTC  
> Closed at: 2017-06-08 03:14:43 UTC  
> Url: https://github.com/boostorg/beast/issues/392  

Now that `basic_fields` does not expose `std::string` in any public interfaces, we can get rid of it and rely only on the allocator to store strings. This might also be a good time to revisit the treatment of multiple fields with the same name. Perhaps they could share ownership of the name string.

---

## Comment 1

> Username: djarek  
> Created at: 2017-05-29 21:01:32 UTC  
> Url: https://github.com/boostorg/beast/issues/392#issuecomment-304729905  

Does this mean that basic_fields will contain non-owning references to header  field-lines contained within the raw message?  How will that work if we mutate the message?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-05-29 21:49:00 UTC  
> Updated at: 2017-05-29 21:49:53 UTC  
> Url: https://github.com/boostorg/beast/issues/392#issuecomment-304735351  

No, it just means that `basic_fields` will use the allocator to manually manage buffers for strings instead of using `std::string`. This is possible now because the container uses `string_view` in its public interfaces. Specifically, `std::string` will be replaced with `std::pair<char*, std::size_t>` (or the equivalent) here:  
https://github.com/vinniefalco/Beast/blob/663c275fc16c2b5e075b96bbe91467f83256df5c/include/beast/http/detail/fields.hpp#L29  
  
Note that nothing stops you from implementing your own **Fields** type that uses non-owning references if you want to!
