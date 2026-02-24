# #427 - Explore string <--> number conversion [Open]

> Username: vinniefalco  
> Created at: 2020-09-28 15:02:07 UTC  
> Updated at: 2024-04-11 18:07:07 UTC  
> Url: https://github.com/boostorg/json/issues/427  

`value_to` (and `number_cast`) could convert string to integer. And we could have a free function that changes a `value` to a string if the number is outside +/- 2^53. This should be explored first.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-04-08 14:34:01 UTC  
> Url: https://github.com/boostorg/json/issues/427#issuecomment-2042918251  

`string::to_number` could become a thing, given Charconv dependency.  
  
```c++  
json::string js = "123";  
auto n = js.to_number<int>();  
```  
  
Another question if we then want to add this converstion to `value::to_number`.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-04-09 19:26:15 UTC  
> Url: https://github.com/boostorg/json/issues/427#issuecomment-2045913646  

I think the idea here is that it would be easier for people who want numbers represented as strings in their JSON. I would just close this and let users ask for it.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-04-11 18:07:06 UTC  
> Url: https://github.com/boostorg/json/issues/427#issuecomment-2050235964  

I vaguely remember that someone actually asked for this, but I can't find an issue, so it probably was a conversation on Slack. @madmongo1, was that you?
