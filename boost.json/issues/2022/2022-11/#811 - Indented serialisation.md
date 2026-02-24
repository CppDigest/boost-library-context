# #811 - Indented serialisation [Open]

> Username: infn-ke  
> Created at: 2022-11-18 17:12:36 UTC  
> Updated at: 2022-11-19 11:51:04 UTC  
> Url: https://github.com/boostorg/json/issues/811  

Can the output from `value_from` be indented to make reading output easier?  
  
```cpp  
std::cout << boost::json::value_from( b ) << std::endl;  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-11-18 17:17:19 UTC  
> Url: https://github.com/boostorg/json/issues/811#issuecomment-1320306879  

`value_from` doesn't output anything, it creates a `value` container. You essentially ask for indented serialisation. It won't be possible with operator<<, but we will think about implementing it for `serialize`.

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-11-18 17:19:37 UTC  
> Url: https://github.com/boostorg/json/issues/811#issuecomment-1320309364  

Meanwhile, here's an example of how to do it manually  
https://www.boost.org/doc/libs/1_80_0/libs/json/doc/html/json/examples.html

---

## Comment 3

> Username: infn-ke  
> Created at: 2022-11-18 21:05:25 UTC  
> Url: https://github.com/boostorg/json/issues/811#issuecomment-1320521040  

> `value_from` doesn't output anything, it creates a `value` container. You essentially ask for indented serialisation. It won't be possible with operator<<, but we will think about implementing it for `serialize`.  
  
I took `value_from` from the docs, that is not working?  
  
https://www.boost.org/doc/libs/develop/libs/describe/doc/html/describe.html#example_to_json  
  
Is there a better way converting from structs to json?

---

## Comment 4

> Username: grisumbras  
> Created at: 2022-11-19 11:51:04 UTC  
> Url: https://github.com/boostorg/json/issues/811#issuecomment-1320868032  

No, why are you asking?
