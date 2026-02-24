# #1239 - How do I initialize a response_parser<file_body>? [Closed]

> Username: alandefreitas  
> Created at: 2018-09-01 03:31:10 UTC  
> Updated at: 2018-09-01 17:01:05 UTC  
> Closed at: 2018-09-01 17:01:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1239  

I know reading to a file with a `response<file_body>` will get the "body limit exceeded".  
  
However, when I change my `file_parser_` from `response<file_body>` to `response_parser<file_body>` I can no longer use `file_parser_.body().open(this->filename_, boost::beast::file_mode::write, ec_file)` to open the file so I get:  
  
```  
Assertion failed: (body_.file_.is_open()), function init, file /usr/local/include/boost/beast/http/basic_file_body.hpp, line 465.  
```  
  
This is because `file_parser_.body()` doesn't exist for `response_parser`.   
  
Is there another function to open the file the `response_parser` will use?   
  
Or do I have to incrementally read to another `parser<string_body>` and manually save it into the file little by little?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-09-01 08:03:00 UTC  
> Updated at: 2018-09-01 08:03:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1239#issuecomment-417841455  

You can access the message stored in the parser by calling `parser::get`:  
  
```  
file_parser_.get().body().open(  
    this->filename_, boost::beast::file_mode::write, ec_file);  
```

---

## Comment 2

> Username: alandefreitas  
> Created at: 2018-09-01 17:01:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1239#issuecomment-417873070  

Thanks!
