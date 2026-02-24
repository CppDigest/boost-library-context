# #438 - Review: names of the encoding/decoding views confusing [Closed]

> Username: alandefreitas  
> Created at: 2022-08-22 05:30:21 UTC  
> Updated at: 2022-08-25 20:40:34 UTC  
> Closed at: 2022-08-25 20:40:34 UTC  
> Url: https://github.com/boostorg/url/issues/438  

> I find the names of the encoding/decoding views confusing. I would  
expect a "decoded_view" to refer to underlying encoded data and  
return equivalent decoded data when it is read from, but you seen to  
call that an "encoded_view".  
  
I agree that some uses might be confusing, like in  
  
```cpp  
pct_encoded_view decoded_host = u.host();  
```  
  
When we replaced `const_string`, everyone who looked at this view proposed one of  `decode_view`, `decoded_view`, `pct_decoded_view`, or `decoded_view`.  
  
If we follow the convention used in `std::ranges`, then it would be `pct_decode_view`, because it's always a verb:  
  
https://en.cppreference.com/w/cpp/ranges/transform_view  
https://en.cppreference.com/w/cpp/ranges#Range_adaptors

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-22 06:38:33 UTC  
> Url: https://github.com/boostorg/url/issues/438#issuecomment-1221914237  

More comments:  
  
> I do feel that `pct_encoded_view` is an unfortunate choice for a view that  
> actively decodes URL-encoded characters. I'd prefer `pct_decode_view` or  
> `pct_decoding_view`. Views names should probably  describe the semantics of the  
> view itself, not what kind of underlying sequence is being adapted/transformed.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-22 16:01:10 UTC  
> Url: https://github.com/boostorg/url/issues/438#issuecomment-1222561994  

More comments:  
  
> I did also find the naming situation for encoded/decoded a bit confusing. In  
> particular, url::path returns pct_encoded_view, but url::encoded_path returns  
> string_view. I would expect the opposite. Maybe this needs additional  
> explanation in the docs?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-23 22:11:03 UTC  
> Url: https://github.com/boostorg/url/issues/438#issuecomment-1224941267  

We are going to do `decode_view`
