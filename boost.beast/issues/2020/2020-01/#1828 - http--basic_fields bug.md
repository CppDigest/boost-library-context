# #1828 - http::basic_fields bug [Closed]

> Username: brjoha  
> Created at: 2020-01-29 21:33:05 UTC  
> Updated at: 2020-02-05 14:40:51 UTC  
> Closed at: 2020-02-05 14:40:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1828  

Using this sequence of actions...  
  
beast::http::request<beast::http::string_body> req;  
req.insert("abc", "1");  
req.insert("abc", "2");  
req.insert("abc", "3");  
  
...a call to req.count("abc") returns 3 as expected.  If we then do the following...  
  
auto iter = req.find("abc");  
req.insert("abc", "4");  
req.erase(iter);  
  
...a call to req.count("abc") returns 0 instead of 3.  However, the header was properly updated since printing the request gives...  
  
 HTTP/1.1  
abc: 1  
abc: 2  
abc: 4

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-01-29 21:35:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1828#issuecomment-579970610  

Code for the tests is here  
https://github.com/boostorg/beast/blob/c9d5049307925bccaf2f5e57c127db1a1c78e178/test/beast/http/fields.cpp#L402

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-01-29 23:03:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1828#issuecomment-580003530  

Hi @brjoha :  
  
That was a great find. Thank you so much for the helpful steps to reproduce.  
  
As you can see above, I have created a test to prove the bug, and its fix.  
  
Keep them coming!  
  
R
