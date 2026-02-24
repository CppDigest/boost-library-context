# #1198 - read headers from request in advanced example [Closed]

> Username: jeffsmith82  
> Created at: 2018-07-18 15:54:06 UTC  
> Updated at: 2019-09-10 22:20:34 UTC  
> Closed at: 2018-08-25 10:37:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1198  

I'm trying to take the advanced server example and send it a header that I then read out but can't work out how.   
  
Is it possible to add an example of this somewhere in the examples as it seems a pretty common thing to do.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-18 17:04:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1198#issuecomment-406004445  

I don't understand what you're trying to do. You want to read the HTTP header first, before reading the body? This is explained in the documentation:  
  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/using_http/parser_stream_operations.html

---

## Comment 2

> Username: jeffsmith82  
> Created at: 2018-07-18 21:00:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1198#issuecomment-406072946  

I was just trying to read a header I was sending   
  
curl command I was using to send the header tester  
  
curl -v -X POST -H "TesTer: That;this" http://127.0.0.1:8080/  
  
What I ended up using to read the header tester.   
  
if(req.method() == http::verb::post){  
                std::cout << req["tester"] << std::endl;  
                return send(bad_request("We dont do that sort of thing here"));  
        }  
  
no idea if this is the correct way of reading headers but it works.  This took me ages to find and I only worked it out after reading another post you made about is_upgrade which has this in.  
  
if(! http::token_list{req[http::field::connection]}.exists("upgrade"))  
  
examples of this would really help in the docs.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-07-18 22:40:05 UTC  
> Updated at: 2018-07-18 22:44:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1198#issuecomment-406096297  

So when you say "read header" what you mean is "access the contents of the header container in the message?" The interface to the fields container is in the documentation:  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields.html

---

## Comment 4

> Username: jeffsmith82  
> Created at: 2018-07-19 09:15:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1198#issuecomment-406210913  

that's what I meant.   
  
I'm trying to say for me at least its much much easier to learn how to use a library with examples rather than purely from the docs. I read that page a few times and still couldn't work it out. until I found code that did it.  
  
Example working code for using all those methods would probably really help people using your library. The best docs I have read always have examples   
  
https://golang.org/pkg/strings/#TrimLeft    
http://php.net/manual/en/function.ltrim.php  
  
Many thanks for the lib by the way. :-)

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-08-18 09:52:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1198#issuecomment-414046186  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-08-25 10:37:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1198#issuecomment-415960045  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 7

> Username: arturbac  
> Created at: 2019-04-25 06:25:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1198#issuecomment-486535944  

I tried get same all day long, till I found this post.  
Also didnt find if there is some way of easy reding GET arguments or i have to do it by myself, and there is no explanation how to implement basic auth.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-04-25 16:30:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1198#issuecomment-486745073  

You have to do it yourself. Beast does not provide an implementation of Basic Authentication.

---

## Comment 9

> Username: arturbac  
> Created at: 2019-04-26 18:48:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1198#issuecomment-487162683  

Sure thx.  
@jeffsmith82 suggestion about adding single line to examples like:  
std::string http_user_agent{ req[http::field::user_agent]  };  
would be a good idea.

---

## Comment 10

> Username: emerth  
> Created at: 2019-09-10 22:10:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1198#issuecomment-530140265  

> I'm trying to say for me at least its much much easier to learn how to use a library with examples rather than purely from the docs. I read that page a few times and still couldn't work it out. until I found code that did it.  
>   
> Example working code for using all those methods would probably really help people using your library. The best docs I have read always have examples  
  
I agree with you. But this is Boost, and Boost doesn't really do documentation! Maybe if documentation were part of the standard, then Boost would implement the best documentation in the world!

---

## Comment 11

> Username: arturbac  
> Created at: 2019-09-10 22:20:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1198#issuecomment-530142915  

> I agree with you. But this is Boost, and Boost doesn't really do documentation! Maybe if documentation were part of the standard, then Boost would implement the best documentation in the world!  
  
Even best code without documentation is useless.  
Good code have to be documented in any way, external doc, comments, examples ...  
If users of code have to spend hours trying to figure out some simple and one of most important things of library that a sign to update the information about how to use the code.
