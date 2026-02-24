# #1023 - [Question] When using direct parsing, what happens if a key in the JSON is not in the struct? [Closed]

> Username: DUOLabs333  
> Created at: 2024-07-12 02:48:18 UTC  
> Updated at: 2024-07-12 11:19:50 UTC  
> Closed at: 2024-07-12 11:19:50 UTC  
> Url: https://github.com/boostorg/json/issues/1023  

Let's say we have struct B. B has three attributes. Now let's say I want to parse_into a string s with 4 keys, and 3 of them are attributes of B. Will the parsing succeed?

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-07-12 07:07:09 UTC  
> Url: https://github.com/boostorg/json/issues/1023#issuecomment-2224960701  

This is considered an error by the parser.

---

## Comment 2

> Username: DUOLabs333  
> Created at: 2024-07-12 10:41:29 UTC  
> Url: https://github.com/boostorg/json/issues/1023#issuecomment-2225305740  

Is there a way to turn it off? I have some JSON with some extra keys I use for logging purposes, but shouldn't matter to the underlying struct, as it could change. As far as I can tell, both glaze and daw_json_link support this, but I would like to not have to add another dependency (I'm only direct parsing for some of the JSON strings).

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-07-12 10:55:47 UTC  
> Url: https://github.com/boostorg/json/issues/1023#issuecomment-2225326284  

The way to allow missing keys is to use an optional as the member's type. E.g.  
```C++  
struct S {  
  int n; // required  
  std::optional<std::string> s; // not required  
};  
```  
  
If that doesn't work for you, you can write a `tag_invoke` overload and convert with `value_to`.

---

## Comment 4

> Username: DUOLabs333  
> Created at: 2024-07-12 11:19:48 UTC  
> Url: https://github.com/boostorg/json/issues/1023#issuecomment-2225361170  

As it turns out, I can rework my system to not have any unused keys, but that could be useful if it later becomes a hard dependency. Thanks.
