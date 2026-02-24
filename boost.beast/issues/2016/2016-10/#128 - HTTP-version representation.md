# #128 - HTTP-version representation [Closed]

> Username: viccpp  
> Created at: 2016-10-13 07:52:50 UTC  
> Updated at: 2017-01-11 00:04:17 UTC  
> Closed at: 2017-01-11 00:04:16 UTC  
> Url: https://github.com/boostorg/beast/issues/128  

Hi, Vinnie!  
  
The version of HTTP is represented in a strange way, as for me, it is `10` or `11`.  I use this way in my library:  
  
```  
//////////////////////////////////////////////////////////////////////////////  
class version  
{  
    unsigned char maj : 4, min : 4;  
public:  
    version() = default;  
    constexpr version(unsigned major, unsigned minor)  
        : maj(major), min(minor) {}  
  
    constexpr unsigned major() const { return maj; }  
    constexpr unsigned minor() const { return min; }  
};  
//////////////////////////////////////////////////////////////////////////////  
  
//----------------------------------------------------------------------------  
constexpr bool operator==(version v1, version v2)  
{  
    return v1.major() == v2.major() &&  
           v1.minor() == v2.minor() ;  
}  
//----------------------------------------------------------------------------  
constexpr bool operator<(version v1, version v2)  
{  
    return v1.major() < v2.major() ||  
           v1.major() == v2.major() && v1.minor() < v2.minor();  
}  
//----------------------------------------------------------------------------  
constexpr bool operator!=(version v1, version v2) { return !(v1 == v2); }  
//----------------------------------------------------------------------------  
constexpr bool operator> (version v1, version v2) { return v2 < v1; }  
//----------------------------------------------------------------------------  
constexpr bool operator<=(version v1, version v2) { return !(v2 > v1); }  
//----------------------------------------------------------------------------  
constexpr bool operator>=(version v1, version v2) { return !(v2 < v1); }  
//----------------------------------------------------------------------------  
```  
  
Then you can write:  
  
`req.version = {1,1};  
`  
  
How about using the same approach in your library?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-13 10:21:09 UTC  
> Url: https://github.com/boostorg/beast/issues/128#issuecomment-253474552  

Hmm...that's a lot of code just to represent a type that, realistically speaking, only has two values (HTTP/1.0 and HTTP/1.1). What is the benefit of that extra code over a simple `int`?

---

## Comment 2

> Username: viccpp  
> Created at: 2016-10-13 10:29:10 UTC  
> Updated at: 2016-10-13 10:32:09 UTC  
> Url: https://github.com/boostorg/beast/issues/128#issuecomment-253476187  

> Hmm...that's a lot of code just to represent a type that, realistically speaking, only has two values (HTTP/1.0 and HTTP/1.1).  
  
HTTP/2 too! And the difference between `10` and `1.0` is big, as for me. You should'nt present to users such ugly hack in the interface.  
  
> What is the benefit of that extra code over a simple int?  
  
Aesthetic at least and practical.  What is the purpose of types in programming languages? Or you prefer typeless approach of langauges as B or Forth - "there is only one type - machine word"?  
  
In this particular case - compactness and decoupling meaning from the actual representation.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-10-13 10:37:46 UTC  
> Url: https://github.com/boostorg/beast/issues/128#issuecomment-253477905  

So you're suggesting that maybe I should represent the version using a `float`?

---

## Comment 4

> Username: viccpp  
> Created at: 2016-10-13 10:38:49 UTC  
> Url: https://github.com/boostorg/beast/issues/128#issuecomment-253478128  

`1.0` is not a float! It is pair of integers

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-10-13 10:42:12 UTC  
> Url: https://github.com/boostorg/beast/issues/128#issuecomment-253478703  

If this issue comes up during the Boost review then I will give it more attention. And I can propose your solution then.

---

## Comment 6

> Username: viccpp  
> Created at: 2016-10-13 10:44:39 UTC  
> Url: https://github.com/boostorg/beast/issues/128#issuecomment-253479135  

Feel free just to close the issue if you don't like the idea. It was only minor suggestion.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-10-13 10:52:08 UTC  
> Url: https://github.com/boostorg/beast/issues/128#issuecomment-253480564  

I think keeping the issue open is a better choice. My opinion is my own, and might not be shared by other users of the library. I'd like all thoughts and discussions on interfaces in Beast to be presented and available to users and more importantly, Boost reviewers. If there is sufficient consensus and interest in adding more structure to the type used to represent the HTTP version, then your generous contribution of code will be helpful in providing a starting point for discussion.

---

## Comment 8

> Username: daniele77  
> Created at: 2016-11-18 10:20:55 UTC  
> Url: https://github.com/boostorg/beast/issues/128#issuecomment-261497542  

I think the library should have a nicer interface. 10 and 11 are so different from "1.0" and "1.1"!  
  
I agree that @2underscores-vic suggestion is a lot of code. But as a library author, you should provide the best interface possible, hiddening the hacks internally, even if it's a lot of code.  It's far better if the code is written once and for all by the library author then each time by every library user.   
  
Anyway: I'd opt for something simpler.  I suggest using an enum { http_1_0, http_1_1, http_2_0 }: it's clear and extensible and it's not too much code.  
  
You can also use a std::string, but since beast currently supports just two values, I'd go for the enum.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2016-11-18 11:42:59 UTC  
> Url: https://github.com/boostorg/beast/issues/128#issuecomment-261513138  

Really, 10 and 11 are "so different" from 1.0 and 1.1? It looks to me like the only difference is a multiplication of 10. How is using a plain `int` a hack? It looks fine to me, you can compare them using `==`, `<`. and `>`. Its simple, doesn't require any additional identifiers. You say the library should have a "nicer" interface but in what way is a simple `int` not "nice?"  
  
You said the code should be written once and not every time by each user. What extra code does an `int` require that users write? A label is just a label, how is `http_1_0` any different from just `10`? It seems like that is just gratuitously adding identifiers. My intent is to keep this simple, without over-engineering when possible.  
  
If there are strong feelings during the Boost review I could be convinced to change it. When Beast comes up on the schedule, you should do a review and voice this concern!

---

## Comment 10

> Username: daniele77  
> Created at: 2016-11-18 13:25:56 UTC  
> Url: https://github.com/boostorg/beast/issues/128#issuecomment-261530906  

Well, it's the main point of having types in the first place...  
Using int's everywhere is possible, but it's advisable to use the type that fits better your data.  
Why?  
Well, compare:  
  
``` C++  
const int version = 42; // my application version  
const int http_version = 11;  
  
req.version = version; // boom  
```  
  
to  
  
``` C++  
const AppVersion version{ 4, 2, 0 };  
const beast::http_version http_version = beast::http_version_1_1; // enum class  
  
req.version = version; // compile time error :-)  
```  
  
As an aside: currently http supports only two (three) versions, but in the general case you cannot be sure the minor be always less than 10. How could you represent e.g., 1.13? and 11.3?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2016-11-18 13:35:14 UTC  
> Updated at: 2016-11-18 13:36:12 UTC  
> Url: https://github.com/boostorg/beast/issues/128#issuecomment-261532772  

> As an aside: currently http supports only two (three) versions, but in the general case you cannot be sure the minor be always less than 10. How could you represent e.g., 1.13? and 11.3?  
  
Beast does not support HTTP versions below 1.0. And it is guaranteed that we will never see versions above 1.1 and below 2.0. Furthermore, the major and minor components of the version are each restricted to a single digit:  
  
From https://tools.ietf.org/html/rfc7230#section-2.6  
  
```  
    HTTP-version  = HTTP-name "/" DIGIT "." DIGIT  
```  
  
As of  HTTP/2, the version field is gone from messages. HTTP/2 implements extensibility through negotiation of individual features. So we will not see HTTP/2.1 or HTTP/3. Instead we will see feature negotiation through specific named fields.  
  
The Beast message model supports all legal HTTP versions as per the BNF above, but realistically speaking, only HTTP versions 1.0, 1.1, and 2.0 are going to ever exist.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-01-11 00:04:16 UTC  
> Url: https://github.com/boostorg/beast/issues/128#issuecomment-271737655  

I'm going to close this for now, if it comes up during the Boost review we can revisit it. Thanks!
