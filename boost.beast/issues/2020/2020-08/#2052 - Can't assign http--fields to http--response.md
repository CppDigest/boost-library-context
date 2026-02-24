# #2052 - Can't assign http::fields to http::response [Closed]

> Username: bog-dan-ro  
> Created at: 2020-08-13 18:37:17 UTC  
> Updated at: 2020-09-15 07:53:47 UTC  
> Closed at: 2020-09-15 07:53:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2052  

I want to update the fields of a `http::response` from another `http::fields` object.  
The following example doesn't work for me:  
```  
http:fields fields = {};// some fields  
http::response<http::string_body> res;  
res = fields; // I got an error here  
```  
I could set them using  
```  
static_cast<http::fields&>(res) = fields;  
```  
but it's ugly ...

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-13 19:15:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2052#issuecomment-673661438  

Hmm. Allowing a direct assignment would make code easier to write but harder to read.  
  
Your workaround actually provides the hint that the assignment is partial.  
  
Ugly it might be, but I think also safer.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-08-13 20:07:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2052#issuecomment-673684868  

`res.base() = fields` but yes that's a code smell

---

## Comment 3

> Username: bog-dan-ro  
> Created at: 2020-08-14 07:16:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2052#issuecomment-673927751  

> `res.base() = fields` but yes that's a code smell  
  
I tried that as well, but it didn't worked for me.  
  
  
> Hmm. Allowing a direct assignment would make code easier to write but harder to read.  
> ...  
  
I thought that the main reason for `http::response` to inherit `http:fields` was to allow this kind of assignments :)  
  
  
IMHO either allow `res = fields;` or add a new method to assign the fields e.g. `res.fields(fields);`

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-08-20 13:37:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2052#issuecomment-677669846  

Hi @bog-dan-ro, when you say it didn't work for you, are you able to show the error output?  
  
@vinniefalco how do we feel about extending the interface of http::message to allow something like:  
  
`msg.assign(fs)` where `fs` is anything `fields`-like?   
  
I am dubious about the advantage.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-08-20 14:47:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2052#issuecomment-677711410  

No we should not extend the interface. The current message container design is flawed, we have to make a new one not add to the existing. There will be no such thing as "fields-like."

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-09-15 07:53:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2052#issuecomment-692536611  

I'm going to close this issue as we don't have plans to extend the interface prior to a refactor.
