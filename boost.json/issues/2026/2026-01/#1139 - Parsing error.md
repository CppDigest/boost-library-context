# #1139 - Parsing error [Closed]

> Username: cdschreiber  
> Created at: 2026-01-21 10:36:00 UTC  
> Updated at: 2026-01-21 11:56:56 UTC  
> Closed at: 2026-01-21 11:56:56 UTC  
> Url: https://github.com/boostorg/json/issues/1139  

auto arr{ boost::json::parse(R"(["Hello",42,true])").as_array() };  
assert( arr[0].as_string() == "Hello" );  
  
fails with exception "value is not a string [boost.json:32 at ./boost/json/impl/value.ipp:425:42 in function 'try_as_string']".

---

## Comment 1

> Username: grisumbras  
> Created at: 2026-01-21 10:41:48 UTC  
> Url: https://github.com/boostorg/json/issues/1139#issuecomment-3777396022  

I suspect, you wrap the value you've got (an array) with another array. Try `assert( arr[0].as_array()[0].as_string() == "Hello" );`  
  
If I am correct, then the problem is using curly braces for construction. My general advice is to NEVER EVER use curlies for construction, unless you want to use a feature that requires them (aggregate initialisation or constructor from `std::initializer_list`).

---

## Comment 2

> Username: cdschreiber  
> Created at: 2026-01-21 11:12:34 UTC  
> Url: https://github.com/boostorg/json/issues/1139#issuecomment-3777523897  

@grisumbras You were exactly right. Thank you very much.
