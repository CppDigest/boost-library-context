# #1425 - Cookies: getting, setting, generating [Open]

> Username: p-groarke  
> Created at: 2019-02-02 07:54:16 UTC  
> Updated at: 2022-12-13 03:31:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1425  

Everyone loves cookies! 🍪 nomnom  
  
I know you've stated before it was out of scope, but that was many years ago. Any chance you can bring this back into scope?  
  
Thx and good day, 🍪

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-02 13:33:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-459965393  

LOL.. yeah, we do like cookies. Yes I can reconsider it, but what do you mean by "cookies?" What precisely do you want Beast to do?

---

## Comment 2

> Username: p-groarke  
> Created at: 2019-02-02 17:42:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-459983999  

I was hoping for a builtin way to create cookies and to get/set them in a http response/request.  
  
I am a complete beginner when it comes http itself (learning with beast), so I have no opinion on how that should look.  
  
Cheers and thx for beast, rawr 🐯

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-02 17:55:47 UTC  
> Updated at: 2019-02-02 17:55:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-459985055  

> I was hoping for a builtin way to create cookies and to get/set them in a http response/request.  
  
Oh, well that's easy, you can do it right now with Beast.  
  
```  
// Set a cookie with the name "mycookie" to the value "123"  
http::response<empty_body> res;  
res.set(field::set_cookie, "mycookie=123");  
```  
  
Now when a browser sends subsequent requests to the server, there will be a "Cookie" field with all the values that the server sent:  
```  
http::request<empty_body> req;  
...  
// Print each cookie in the request  
for(auto param : http::param_list(req[field::cookie]))  
    std::cout << "Cookie '" << param.first << "' has value '" << param.second << "'\n";  
```  
  
Hope this helps!

---

## Comment 4

> Username: p-groarke  
> Created at: 2019-02-02 18:03:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-459985668  

Yes a lot! Beast already supports get/set of cookies, very nice :)  
  
Then I'd rephrase my feature request as "cookie generator", to easily create cookies. Though I'm feeling you'll respond out-of-scope, probably rightly so.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-02-02 18:05:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-459985921  

What does "cookie generator" mean? Maybe we can enlarge the scope... I'm feeling generous. But seriously, I am generally speaking widening the scope of Beast (although, I am doing it carefully and conservatively).

---

## Comment 6

> Username: p-groarke  
> Created at: 2019-02-02 18:15:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-459986656  

You may close this if you want, I feel I need to learn more before I can be of much help to you.  
  
Basically I was thinking of a cookie factory with serialization/deserialization to http. I do think you'd want to enlarge the scope, as cookies are probably not the only thing that need factory. I'm all for widening the scope of beast, because it is pretty badass so far.  
  
So pseudo code:  
```  
// value and onwards are optional  
cookie c{name, value, expire, path, domain, secure, httponly};  
http::response<empty_body> res;  
res.set(field::set_cookie, c);  
```  
  
Is this realistic?  
Cheers

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-02-02 18:57:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-459990025  

This sounds pretty reasonable, it could go into `<boost/beast/_experimental/http/cookie.hpp>` at least, it would need an example program probably a client that connects to a predefined list of public hosts (google, apple, etc) and tries to get cookied up, then prints them. It would of course need tests. But before we get to that this needs more work, what does the declaration of `cookie` look like? Is there a link to the cookie serialization format (an RFC maybe)?

---

## Comment 8

> Username: p-groarke  
> Created at: 2019-02-02 19:49:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-459993859  

I believe this is the one https://tools.ietf.org/html/rfc6265

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-02-02 20:06:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-459995233  

> https://tools.ietf.org/html/rfc6265  
  
Whoa... that is definitely non-trivial. In fact it looks harder to parse than the entire HTTP message itself! Parsing RFC-compliant timestamps is, as we say in the biz "a pain in the arse." This is still doable but it is a decent sized effort.

---

## Comment 10

> Username: p-groarke  
> Created at: 2019-02-02 20:11:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-459995578  

I don't know how much you can depend on other libraries now that beast is part of boost, but would Hinnant's `date` library help here? Anyhow, I won't hold my breath. I guess the RFC has made my point it would be nice to have a compliant cookie generator in beast :)

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-02-02 20:46:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-459997819  

I'm sure Howard wouldn't mind if I borrowed some code but we can't just depend on the entire thing. And for security purposes we really need something that is built in a certain way, to minimize dependencies, in a way that is also friendly to being audited. Maybe there is something out there. Unfortunately I don't have the time to dive into it right now but I am thinking that yes it could be in-scope for _experimental, and if that is successful then in-scope for the public interfaces.

---

## Comment 12

> Username: HowardHinnant  
> Created at: 2019-02-02 21:20:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-460000110  

If it helps, roll-your-own `{year, month, day} <-> Unix Time` conversion algorithms documented here, public domain: http://howardhinnant.github.io/date_algorithms.html

---

## Comment 13

> Username: vinniefalco  
> Created at: 2019-02-02 21:33:33 UTC  
> Updated at: 2019-02-02 21:33:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-460000959  

> http://howardhinnant.github.io/date_algorithms.html  
  
Amazing code as usual, but.... there's nothing here for parsing dates and times from strings?

---

## Comment 14

> Username: HowardHinnant  
> Created at: 2019-02-02 21:35:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-460001052  

Correct, bring your own parsing.  The algorithms are just the math.

---

## Comment 15

> Username: wangh09  
> Created at: 2019-04-27 02:40:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-487248023  

Hi, is there a way to read cookie from the websocket session? thanks!

---

## Comment 16

> Username: vinniefalco  
> Created at: 2019-04-27 04:10:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-487252878  

> is there a way to read cookie from the websocket session?  
  
Yes, see:  
  
https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/using_websocket/handshaking.html#beast.using_websocket.handshaking.inspecting_http_requests

---

## Comment 17

> Username: wangh09  
> Created at: 2019-04-28 04:06:00 UTC  
> Updated at: 2019-04-28 04:10:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-487341335  

Thanks, it works.   
BTW I found that the param_list requires a ';' at the beginning,  namely ";key1=val1; key2=val2" is valid but "key1=val1; key2=val2" is not.

---

## Comment 18

> Username: vinniefalco  
> Created at: 2019-04-28 15:33:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-487389838  

> I found that the param_list requires a ';' at the beginning  
  
Yes, that is required by RFC7230's BNF:  
  
```  
        param-list  = *( OWS ";" OWS param )  
        param       = token OWS [ "=" OWS ( token / quoted-string ) ]  
```

---

## Comment 19

> Username: maddanio  
> Created at: 2021-05-20 08:51:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-844876930  

> > is there a way to read cookie from the websocket session?  
>   
> Yes, see:  
>   
> https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/using_websocket/handshaking.html#beast.using_websocket.handshaking.inspecting_http_requests  
  
but is there any way to set them on the client end? we would like to implement uniform auth on our apache (which proxies all our ws services) and the easiest way is request headers or cookies. in js cookies is the way since we cant set headers. but in beast it seems we can do neither?

---

## Comment 20

> Username: madmongo1  
> Created at: 2021-05-20 11:49:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-845023314  

A client side cookie jar is beyond the scope of beast.  
You would need to find an existing library or write what you need.

---

## Comment 21

> Username: maddanio  
> Created at: 2021-05-20 11:56:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-845029053  

> > > is there a way to read cookie from the websocket session?  
> >   
> >   
> > Yes, see:  
> > https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/using_websocket/handshaking.html#beast.using_websocket.handshaking.inspecting_http_requests  
>   
> but is there any way to set them on the client end? we would like to implement uniform auth on our apache (which proxies all our ws services) and the easiest way is request headers or cookies. in js cookies is the way since we cant set headers. but in beast it seems we can do neither?  
  
I take it back, there is the decorator option which does what I need, just didnt understand that, my error.

---

## Comment 22

> Username: dhalsimax  
> Created at: 2021-06-14 17:11:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-860849146  

http::param_list has another anomaly.   
In case you pass strings like: "; Coockie1=;Cookie2=Value2" where a key has got no value, it returns an empty set. This could be ok if you know, but would be nice if it returns all the keys value pairs with the "it->second" sets to "" whener it is. Firefox for example refires cookies with null values, so in case you set one of this in the response you will be forces to restart the browser. In another case you set "Expiration" attribute of such null value cookies you will be also forced to manually clear cookies on browser. Anyway rfc, as I remember, doesn't  forbid such kind of cookies and could be useful to have some of null valued.

---

## Comment 23

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:07:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-1256876271  

As the cookie jar is beyond the scope of beast, can this be closed?

---

## Comment 24

> Username: tyyykkkeeessshhaaaa  
> Created at: 2022-12-09 22:34:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-1344852698  

> > > is there a way to read cookie from the websocket session?  
> >   
> >   
> > Yes, see:  
> > https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/using_websocket/handshaking.html#beast.using_websocket.handshaking.inspecting_http_requests  
>   
> but is there any way to set them on the client end? we would like to implement uniform auth on our apache (which proxies all our ws services) and the easiest way is request headers or cookies. in js cookies is the way since we cant set headers. but in beast it seems we can do neither?  
  
If you are finished or have a working sample of your code for proxies with websockets, please send 😄 😄 😄

---

## Comment 25

> Username: tyyykkkeeessshhaaaa  
> Created at: 2022-12-09 22:35:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-1344852938  

need to implement proxies into my client

---

## Comment 26

> Username: tyyykkkeeessshhaaaa  
> Created at: 2022-12-10 21:50:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-1345391509  

im interested in getting cookies from a response, haven't seen an example of this

---

## Comment 27

> Username: klemens-morgenstern  
> Created at: 2022-12-11 09:56:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-1345508173  

It's in the works in my [requests](https://github.com/klemens-morgenstern/requests) library on the client side.

---

## Comment 28

> Username: vinniefalco  
> Created at: 2022-12-11 21:14:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-1345658204  

This is how you generate the cookies  
https://www.wideopeneats.com/our-grandmas-classic-chocolate-chip-cookies/  
![image](https://user-images.githubusercontent.com/1503976/206929113-1ca662b7-999a-4ae5-8d9f-d81916d65a64.png)

---

## Comment 29

> Username: p-groarke  
> Created at: 2022-12-11 21:21:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-1345659684  

tsssk, unfair  
  
![Screenshot 2022-12-11 16 20 32](https://user-images.githubusercontent.com/156883/206929413-6806ffdd-cbdb-498a-be93-4a649c1a7d00.png)

---

## Comment 30

> Username: vinniefalco  
> Created at: 2022-12-11 21:31:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-1345661604  

Oops! Didn't even think of that. Here's a softcopy of the recipe  
![image](https://user-images.githubusercontent.com/1503976/206929912-d94362c9-8778-40e4-a454-fd77a3bab2c2.png)

---

## Comment 31

> Username: tyyykkkeeessshhaaaa  
> Created at: 2022-12-12 01:16:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-1345733452  

thank you, i have found a new and better passion, screw ssl and websocket in C++

---

## Comment 32

> Username: tyyykkkeeessshhaaaa  
> Created at: 2022-12-12 01:21:33 UTC  
> Updated at: 2022-12-12 01:25:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-1345736672  

> It's in the works in my [requests](https://github.com/klemens-morgenstern/requests) library on the client side.  
  
looks, very nice! will definitely use if possible in my code, although not entirely sure what libraries  are best for me.  
If anyone can provide guidance as I need a way to be able to create several Https clients and websocket clients and have them in separate threads, also needs to be able to modify cookies and headers for requests too and get them from response  
  
![image](https://user-images.githubusercontent.com/119077174/206941869-0d97d77d-15e4-4325-ad18-9f762205946e.png)  
for more context this is essentially for web scraping  
  
edit: http is supposed to say https in visualization

---

## Comment 33

> Username: vinniefalco  
> Created at: 2022-12-12 01:31:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-1345743751  

there's a beast example that launches a whole bunch of clients and tries to download the home page of the top 10,000 most popular web addresses:  
  
https://github.com/boostorg/beast/tree/develop/example/http/client/crawl

---

## Comment 34

> Username: tyyykkkeeessshhaaaa  
> Created at: 2022-12-13 03:31:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1425#issuecomment-1347696413  

> there's a beast example that launches a whole bunch of clients and tries to download the home page of the top 10,000 most popular web addresses:  
>   
> https://github.com/boostorg/beast/tree/develop/example/http/client/crawl  
  
yes, I have seen this and actually used it in my initial code using this library, however it still lacks proxies with websockets and cookies
