# #189 - docs: avoid catch-and-ignore examples [Closed]

> Username: akrzemi1  
> Created at: 2022-06-06 20:57:35 UTC  
> Updated at: 2022-06-09 20:07:22 UTC  
> Closed at: 2022-06-09 20:07:22 UTC  
> Url: https://github.com/boostorg/url/issues/189  

Avoid examples as in https://github.com/CPPAlliance/url/blob/develop/test/unit/snippets.cpp#L45-L52   
```c++  
        try  
        {  
            url_view u = parse_uri(s).value();  
        }  
        catch (std::invalid_argument const&)  
        {  
            // handle error  
        }  
```  
  
This example is against the intended usage of the function, and often an exception-handling anti-pattern. Instead use:  
  
```c++  
        url_view u = parse_uri(s).value(); // throws std::invalid_argument if parsing failed  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-06 20:59:51 UTC  
> Url: https://github.com/boostorg/url/issues/189#issuecomment-1147924853  

Is this better though? We avoid the "anti-pattern" but it is also less legible, because a comment does not speak as precisely as C++ code.

---

## Comment 2

> Username: sehe  
> Created at: 2022-06-06 21:04:00 UTC  
> Url: https://github.com/boostorg/url/issues/189#issuecomment-1147929057  

Yeah. I see both as completely equal. One is shorter though (just saying)

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-06-06 21:13:56 UTC  
> Url: https://github.com/boostorg/url/issues/189#issuecomment-1147937597  

I've been thinking about this since it came up on the ML.   
  
We have the `snippet_parsing_3`  
  
```cpp  
url_view u = parse_uri( s ).value();  
```  
  
and `snippet_parsing_4`:  
  
```cpp  
try  
{  
    url_view u = parse_uri(s).value();  
}  
catch (std::invalid_argument const&)  
{  
    // handle error  
}  
```  
  
I don't think we *have* to replace anything like the `snippet_parsing_4` anti-pattern because the documentation is not a programming course or anything. And it's almost impossible to come up with lots of examples that don't use that pattern without making the documentation unnecessarily verbose.   
  
But I do think we could and should simply *remove* snippets like the `snippet_parsing_4`. Not because we can't use them but because they are almost useless:  
  
```cpp  
url_view u = parse_uri( s ).value();  
```  
  
already contains almost the same information. We already mentioned in the docs that `value()` is going to throw when we have a problem and we should be able to assume the user knows how to handle exceptions.
