# #701 - P0083r3 for basic_fields [Open]

> Username: arun11299  
> Created at: 2017-07-30 08:10:54 UTC  
> Updated at: 2017-08-01 13:23:39 UTC  
> Url: https://github.com/boostorg/beast/issues/701  

Version: 90  
  
Just wanted to know if a "merge" API for header would make a sense or not. I have a situation where I need to have some sort of default set of headers which can either be customised by a client code by providing another header. So, instead of doing the regular (and inefficient) way of iterating and performing a "set", using the "merge" operation of intrusive list would be better.  
  
WDYT ?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-30 12:45:24 UTC  
> Updated at: 2017-07-30 12:45:35 UTC  
> Url: https://github.com/boostorg/beast/issues/701#issuecomment-318899392  

I don't understand what you're trying to do. Could you please provide a very simple piece of example code?

---

## Comment 2

> Username: arun11299  
> Created at: 2017-07-31 07:17:13 UTC  
> Updated at: 2017-07-31 07:17:57 UTC  
> Url: https://github.com/boostorg/beast/issues/701#issuecomment-318988359  

Yeah, I think a pseudo code would be good.  
  
// Client side code  
```  
Request r;  
beast::http::request_header<> h;  
//...fill headers here  
r.send(h);  
OR  
r.send({ {"e", "f"}, {"a", "z"} });  
```  
  
// Library code  
```  
class Request  
{  
public:  
    Request() {  
        fill_in_defaults();  
    }  
  
    void send(const std::initializer_list<std::pair<beast::string_view, beast::string_view>>&);  
    void send(const beast::http::request_header<>&);  
  
private:  
    void fill_in_defaults() {  
        headers_.set("a", "b");  
        headers_.set("c", "d");  
     }  
  
private:  
    beast::http::request_header<> headers_;  
};  
```  
  
Assume that the send operation creates the complete request and dispatches it and it has 2 overloads, one taking an initializer-list and the other taking a beast header itself.  
  
So, in the overload taking the `beast::http::request_header`, what would be the most efficient way to merge it with the `headers_` member ? I would think the `intrusive_list` merge operation would be good to have in such cases.  
  
Please let me know if there is anything I can add to make it more clear.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-31 14:13:53 UTC  
> Updated at: 2017-07-31 14:16:04 UTC  
> Url: https://github.com/boostorg/beast/issues/701#issuecomment-319079215  

How would you feel about something like this:  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0083r3.pdf  
  
Example of use:  
http://en.cppreference.com/w/cpp/container/map/merge

---

## Comment 4

> Username: arun11299  
> Created at: 2017-08-01 07:25:41 UTC  
> Url: https://github.com/boostorg/beast/issues/701#issuecomment-319290585  

Hmm..yes. That is a cool feature. But since beast is making use of `boost::intrusive_list`, the functionality is already there. Or are you planning to move to std::containers ?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-08-01 13:02:45 UTC  
> Updated at: 2017-08-01 13:13:37 UTC  
> Url: https://github.com/boostorg/beast/issues/701#issuecomment-319363516  

I'm saying that `http::basic_fields` could offer the same feature as described in P0083r3. So you would have `basic_fields::node_handle`, `basic_fields::extract()`, and `basic_fields::merge()`.

---

## Comment 6

> Username: arun11299  
> Created at: 2017-08-01 13:13:11 UTC  
> Url: https://github.com/boostorg/beast/issues/701#issuecomment-319365920  

Oh..ok. That would be nice.
