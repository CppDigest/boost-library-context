# #1885 - About param_list [Closed]

> Username: ErikvLinstee  
> Created at: 2020-03-29 12:18:36 UTC  
> Updated at: 2020-04-09 11:06:15 UTC  
> Closed at: 2020-04-09 11:06:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1885  

Parameters of HTTP header fields have been defined by param_list as:  
> a series of name/value pairs with each pair starting with a semicolon  
  
But aren't they supposed to be defined as:  
>  a series of name/value pairs separated by a semicolon  
  
The first definition fails to parse "abc=1; cdef".  
  
For instance, how would I go about parsing the header field "Cookie: Version=1; Skin=new"?  
  
I would start by getting the field value from the message by its field name cookie, like so:  
> auto cookie = message[http::field::cookie];  
  
This would leave me with "Version=1; Skin=new".  
Next, I want to iterate over the parameters "Version=1" and "Skin=new". The param_list fails me here, because it wants it argument to start with ";".

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-03-29 13:27:16 UTC  
> Updated at: 2020-03-29 13:27:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1885#issuecomment-605635495  

Hi @ErikvLinstee ,  
  
The type param_list was not actually intended to be a public type. To quote @vinniefalco , the library's author:  
  
> if I had a do-over I would not make them public  
> but since they are already public, they should work and have proper docs  
> The names of the classes reflect the names in the BNF in the HTTP spec  
  
So, while I will get to work resolving the documentation and checking functionality, it's probably a good idea for you not to use them in user code.  
  
If you know the format of the field you are parsing (for example `;`-separated) a reasonable workaround for now would be to use the boost::tokenizer class:  
  
```  
#include <boost/beast/http.hpp>  
#include <boost/tokenizer.hpp>  
#include <boost/algorithm/string.hpp>  
  
void handle_cookie(boost::beast::string_view cookie)  
{  
    using tokenizer = boost::tokenizer<boost::char_separator<char>>;  
    const auto sep = boost::char_separator<char>(";");  
    for (auto tok : tokenizer(cookie, sep))  
    {  
        boost::trim(tok);  
        // use the token  
    }  
}  
```

---

## Comment 2

> Username: ErikvLinstee  
> Created at: 2020-03-29 13:58:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1885#issuecomment-605640166  

Thanks, I won't use them then.  
The tokenizer will do fine.  
  
As a side note. I need to parse structured body data, such as multipart content-types. This involves some of the same processing as the library is doing for the start line, the header fields and the chunks. Could some of the helper functions be made public to avoid code duplication?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-03-29 14:35:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1885#issuecomment-605646149  

> Could some of the helper functions be made public to avoid code duplication?  
  
Definitely not, because that would constrain future changes to the implementation.  
  
As for the `param_list` code, feel free to copy it and modify it to suit your needs.

---

## Comment 4

> Username: ErikvLinstee  
> Created at: 2020-03-30 08:30:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1885#issuecomment-605858453  

> Definitely not, because that would constrain future changes to the implementation.  
  
I agree, that would not be good. But I was thinking of useful helpers like is_digit and trim_front. Although these are rather simple and not that hard to duplicate. Especially with copy&paste :-)
