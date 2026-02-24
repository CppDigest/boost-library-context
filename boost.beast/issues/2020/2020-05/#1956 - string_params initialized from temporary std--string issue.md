# #1956 - string_params initialized from temporary std::string issue [Closed]

> Username: jselbie  
> Created at: 2020-05-16 05:16:33 UTC  
> Updated at: 2020-06-12 19:20:32 UTC  
> Closed at: 2020-06-12 19:20:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1956  

Version: Boost 1.73.0.  
  
We have an internal class that gets filled out by feature code that represents the HTTP response to handoff to the Beast layer.  Here's a short summary:  
  
```  
class Response  
{  
public:  
    std::string GetContentType()  
    {  
        return "application/json";  
    }  
};  
```  
  
And then in the class that serves the response, there's something that looks like this:  
  
```  
void HttpSession::CompleteRequest(shared_ptr<Response>& response)  
{  
    ...  
    // _res is of type boost::beast::http::response<boost::beast::http::string_body>  
    _res.set("Content-Type", response->GetContentType());  
    ...  
}  
```  
  
So far, so good.  However, our build environment's linter kicked in and gave a warning about an implicit constructor call on the `_res.set` line above.  I honestly didn't study what it was warning about. I just observed that the second parameter to `response::set` is a `string_param` reference and I was passing an `std::string`.  Not knowing what a string_param actaully was, I satisfied the warning with this change:  
  
```  
boost::beast::string_param contentType = response->GetContentType();  
_res.set("Content-Type", contentType);  
````  
  
Compiler happy.   Linter happy.  Yet, there's a bug that got introduced here that the unit tests fortunately caught.  
  
That `string_param contentType` is really a wrapper for a `string_view` that references the pointer to the temporary `std::string` returned by `GetContentType`  By the time `_res.set` is invoked on the subsequent line, that string's internal pointer has been deleted and the `string_param` is referencing deleted memory.  Garbage gets passed to the actual header table in the response map.  
  
The workaround was simply this:  
  
```  
std::string tmp = response->GetContentType()  
boost::beast::string_param contentType = tmp;  
_res.set("Content-Type", contentType);  
```  
  
Linter issue aside, my feedback to this whole thing is that given the mountain of template types in Beast surrounding buffers and strings, it's surprisingly difficult for a developer to grok the purpose and lifetime requirements of each.  `response::set` does not have an overload declared as `void set(const string& key, const string& value)`, which is what most developers would probably expect.    A string copy happens either way, so why not just expose the more universal objects as parameters?  
  
Thanks for listening.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-05-16 06:02:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1956#issuecomment-629594229  

Hmm. A few things to say here.  
  
Firstly, the implicit construction of a string_view does not seem to me like something a linter should be complaining about. The whole point of a string view is that it implicitly converts any string-like thing to a single reference type for passing to APIs.  
  
Secondly, I have ranted before about the fact that string_view’s constructor not being marked explicit. It seems I have a difference of view with the original inventors of the type. They wanted it to be implicitly convertible from anything so that they wouldn’t have to name it when passing various string types to an api that accepts a string_view.  
  
On the other hand I personally regard string_view as a dangerous reference type masquerading as a value, which should never appear in user code.  
  
I think a better workaround would be to avoid the use of the intermediate variable ‘tmp’ altogether and simply provide the explicit cast.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-05-16 06:45:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1956#issuecomment-629598156  

I agree `string_param` is pointless and we should get rid of it. The idea was to support conversions, for example passing an integer, and the library would convert that to the correct string. However as Richard pointed out, doing away with `string_param` and putting `string_view` in its place is going to have a similar problem.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-05-16 06:46:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1956#issuecomment-629598278  

> A string copy happens either way, so why not just expose the more universal objects as parameters?  
  
Because Beast does not store the headers using `std::string`. It stores them in an internal, implementation-defined representation which is subject to change and not visible to callers. Using `std::string` as the parameter type would force a copy or dynamic allocation needlessly.

---

## Comment 4

> Username: pdimov  
> Created at: 2020-05-16 14:20:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1956#issuecomment-629653412  

The whole point of `string_view` is to be used as a parameter so that you don't need to write overloads for all string types. Making it explicit would entirely defeat its purpose.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-05-16 15:37:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1956#issuecomment-629664648  

The actionable thing here is that `string_param` should be deprecated and removed
