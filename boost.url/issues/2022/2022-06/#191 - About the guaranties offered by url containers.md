# #191 - About the guaranties offered by url containers [Closed]

> Username: akrzemi1  
> Created at: 2022-06-08 14:36:45 UTC  
> Updated at: 2022-06-08 18:53:14 UTC  
> Closed at: 2022-06-08 18:53:13 UTC  
> Url: https://github.com/boostorg/url/issues/191  

The docs say:  
  
> Another feature of the library is that all container mutations leave the URL in a valid state.  
  
This seems a useful guarantee; but I think this needs more clarification. Why is this guarantee useful and for who? My intuitive understanding is that while I modify an `url`, at some point I will need to convert it to a string and pass it as part of HTTP protocol, and at this point I do not want to produce something incompatible with HTTP protocol.  
  
But the statement is saying something more.  
  
```c++  
urls::url u; // creation  
u = {};      // mutation  
```  
  
This stores an empty string which according to the comments in https://github.com/CPPAlliance/url/blob/develop/include/boost/url/url.hpp#L152, is a valid representation of a one of the four syntaxes: the one called "relative-ref". But what good is it for me?  
  
Does anyone need to use a "relative-ref"-like URL variant? Is it one of the intended usages?  
  
Or to put it in a different way? Is it any value for me that `u` stores a valid representation of one of the four grammar productions, if I do not know which one it is?  
  
Or maybe what you say is that all the `set_` functions guarantee no "URL injection"?

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-06-08 16:12:20 UTC  
> Url: https://github.com/boostorg/url/issues/191#issuecomment-1150119704  

> produce something incompatible with HTTP protocol  
  
This is not a huge advantage anyway because you need something 1) not incompatible with HTTP protocol and 2) correct for your application. Replacing the proper URL with the valid URL `""` is probably not going to help much.  
  
> is a valid representation of a one of the four syntaxes: the one called "relative-ref". But what good is it for me?  
  
I don't think this is a good way to look at it. Being a valid URL is just an invariant of the class. For instance,  
  
```cpp  
std::vector<int> v; // creation  
v = {};                    // mutation  
```  
  
stores a representation of a valid empty sequence of elements at the end. Because sequences of elements are just what the class vector represents.  
  
Then what good is it for me that a vector doesn't break the invariant of being what "vector" means?   
  
From the perspective of the user, we could say it's "good" that:  
  
- I'm using a class that's always semantically correct so I don't need to keep calling some `u.valid()` function to check if any input URL is in a valid state, and  
- I'm using a class that's not internally doing extra work to repeatedly check if its invariants are broken.   
  
That's implied for almost any class that is well designed. Allowing the class to break its invariants is what requires better evidence.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-08 16:29:39 UTC  
> Url: https://github.com/boostorg/url/issues/191#issuecomment-1150137455  

> Does anyone need to use a "relative-ref"-like URL variant? Is it one of the intended usages?  
  
Well yes, you will use a relative-ref since that's how you resolve the HTTP GET request-target against the document root (if you represent the document root using the file scheme). The empty string is a valid relative-ref, but HTTP carves it out with a special rule:  
  
From https://datatracker.ietf.org/doc/html/rfc7230#section-5.3.1  
> If the target URI's path component is empty, the client MUST send "/" as the path within the origin-form of request-target  
  
Producers of HTTP request-target URLs are responsible for meeting this requirement. Consumers of HTTP request-target URLs use the function `is_path_absolute` to determine if the requirements for _absolute-form_ are met:  
  
https://master.url.cpp.al/url/ref/boost__urls__url_view/is_path_absolute.html  
  
See also: https://github.com/CPPAlliance/url/issues/193

---

## Comment 3

> Username: akrzemi1  
> Created at: 2022-06-08 17:59:34 UTC  
> Url: https://github.com/boostorg/url/issues/191#issuecomment-1150225239  

Thanks for the explanation. Let me then state a hypothesis, and I would like to know what you think of it.  
  
In an alternate implementation we could have two types:  
  
* `url` for storing absolute URLs, and  
* `relative_url` for storing relative-refs.  
  
This is based on the assumption (please correct me if the assumption is wrong), that at every point of my program, I always know when I expect an absolute path and when I expect a relative path; and this separation enables me to (1) have stronger invariants in `url` and `relative_url` (2) avoid bugs where I use a relative URL when I meant to use an absolute one.  
  
Is there a case where I am fine with using either a "relative-ref" or an "absolute-URI"?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-06-08 18:09:46 UTC  
> Updated at: 2022-06-08 18:10:35 UTC  
> Url: https://github.com/boostorg/url/issues/191#issuecomment-1150234554  

No I don't like this at all, I wanted to avoid a proliferation of container types. The way to ensure that you are only receiving a relative path is to use the corresponding parsing function. If you don't know anything about the provenance of the URL you can call `is_path_absolute`. But baking it into the type system just to represent one bit of information doesn't strike me as a good design choice.   
  
There are times when you might start with an absolute-URI and mutate it to become a relative-ref. For example by removing a common prefix and explicit scheme (when the scheme is implied, such as in HTTP). And it is common to go in the opposite direction, such as resolving a relative ref against a base URI to produce an absolute-URI. If we make the types different this would become needlessly complex.  
  
I hear what you're saying about avoiding bugs but I don't think we gain enough to make it a distinct type. Remember that we would also need the `relative_view`, and the `static_relative_ref`, and eight new range-like containers to represent the matrix of possible path segments and query parameters. So the current design choice represents a necessary compromise among all the considerations.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2022-06-08 18:53:13 UTC  
> Url: https://github.com/boostorg/url/issues/191#issuecomment-1150277542  

Thanks. This makes sense.
