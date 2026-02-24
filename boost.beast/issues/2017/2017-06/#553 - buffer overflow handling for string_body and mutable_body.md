# #553 - buffer overflow handling for string_body and mutable_body [Closed]

> Username: octopus-prime  
> Created at: 2017-06-28 20:21:39 UTC  
> Updated at: 2017-07-03 21:51:56 UTC  
> Closed at: 2017-07-03 21:51:56 UTC  
> Url: https://github.com/boostorg/beast/issues/553  

Beast/v70  
  
Was looking at test coverage to find untested parts to increase coverage.  
So i discovered this:  
  
```  
            try  
            {  
                body_.resize(len + n);  
            }  
            catch(std::length_error const&)  
            {  
                ec = error::buffer_overflow;  
                return;  
            }  
            ec.assign(0, ec.category());  
```  
  
There you catch `std::length_error` only.  
What about the exceptions thrown by corresponding Allocator?  
e.g. `std::bad_alloc`  
  
http://en.cppreference.com/w/cpp/string/basic_string/resize

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-28 20:28:35 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311779365  

They are not caught!

---

## Comment 2

> Username: octopus-prime  
> Created at: 2017-06-28 20:29:34 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311779601  

Is this good or bad?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-28 20:40:51 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311782930  

I think its bad, what do you propose for the fix? What exceptions should it catch, and what errors should it map them to?

---

## Comment 4

> Username: octopus-prime  
> Created at: 2017-06-28 20:42:41 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311783527  

I would suggest catching `std::exception` and always mapping to `error::buffer_overflow`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-28 20:43:51 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311783889  

I'm okay with that, would you like to submit a pull request? `mutable_body` needs it too (I think)

---

## Comment 6

> Username: octopus-prime  
> Created at: 2017-06-28 20:50:16 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311785902  

Yes `mutable_body` has code duplicated from `string_body`, since Reader and Writer are exactly the same... So they should use the same Reader/Writer in future?!  
  
I can do the fixes and making a pull request.  
  
What about this:  
  
```  
            if(content_length)  
            {  
                if(*content_length > (std::numeric_limits<std::size_t>::max)())  
                {  
                    ec = make_error_code(errc::not_enough_memory);  
                    return;  
                }  
                ec.assign(0, ec.category());  
                body_.reserve(static_cast<std::size_t>(*content_length));  
            }  
            else  
            {  
                ec.assign(0, ec.category());  
            }  
```  
  
There is no try/catch at all.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-06-28 20:53:56 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311787103  

>So they should use the same Reader/Writer in future?!  
  
If you mean that `string_body` should include the header file for `mutable_body` or vice versa, no, because library code cannot use code from `example/`. It has to be duplicated. I think that's okay.  
  
>There is no try/catch at all.  
  
Looks like another area that needs `try` and `catch`

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-06-28 20:55:10 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311787509  

By the way, these are the kinds of bugs we like, easy to fix and doesn't change any public interfaces. As long as there is no major design problem with interfaces, then we're in good shape - things like this can be cleaned up over time (not saying we shouldn't fix it now but this is not as severe as an interface design flaw).

---

## Comment 9

> Username: octopus-prime  
> Created at: 2017-06-28 21:18:22 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311794950  

So you create a new branch first?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-06-28 21:20:58 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311795632  

Yeah just make a new branch in your fork, based off v70 and place the commit which edits both classes (don't forget the CHANGELOG.md)

---

## Comment 11

> Username: octopus-prime  
> Created at: 2017-06-28 21:24:00 UTC  
> Updated at: 2017-06-28 21:24:50 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311796352  

v70 is master now, isn't it?  
I think i merged...

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-06-28 21:25:45 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311796765  

Yeah it merged, I just updated the docs. No worries! Maybe its time to create a **develop** branch.. heh

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-06-28 21:26:26 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311796926  

Yes, we need a **develop** branch because I am not supposed to make changes to **master** during the formal review. I will create it now.

---

## Comment 14

> Username: octopus-prime  
> Created at: 2017-06-28 21:30:25 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311797875  

So i should wait for develop branch to base on it?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-06-28 21:31:09 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311798044  

>So i should wait for develop branch to base on it?  
  
No, because the **develop** branch will be identical to **master** right now. Just make a new branch in your fork, based on **master** (which is v70).

---

## Comment 16

> Username: octopus-prime  
> Created at: 2017-06-28 21:56:18 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311803751  

Is it okay if all problems with length are mapped to  
`ec = error::buffer_overflow;`  
?

---

## Comment 17

> Username: octopus-prime  
> Created at: 2017-06-28 21:57:21 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311803978  

```  
        template<bool isRequest, class Fields>  
        explicit  
        writer(message<isRequest, string_body, Fields>& m,  
            boost::optional<std::uint64_t> content_length,  
                error_code& ec)  
            : body_(m.body)  
        {  
            if(content_length)  
            {  
                if(*content_length > (std::numeric_limits<  
                        std::size_t>::max)())  
                {  
                    ec = error::buffer_overflow;  
                    return;  
                }  
                try  
                {  
                    body_.reserve(static_cast<  
                        std::size_t>(*content_length));  
                }  
                catch(std::exception const&)  
                {  
                    ec = error::buffer_overflow;  
                    return;  
                }  
            }  
            ec.assign(0, ec.category());  
        }  
```

---

## Comment 18

> Username: vinniefalco  
> Created at: 2017-06-28 21:57:31 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311804012  

I can't think of anything wrong with that. Its better than not catching the exception. And we can always change the error later if we want something better.

---

## Comment 19

> Username: octopus-prime  
> Created at: 2017-06-28 22:01:39 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311804942  

Is "Fix buffer overflow handling for string_body" as commit message okay?

---

## Comment 20

> Username: vinniefalco  
> Created at: 2017-06-28 22:03:46 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-311805379  

Yes that's great

---

## Comment 21

> Username: octopus-prime  
> Created at: 2017-06-29 17:53:48 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-312045445  

okay. would like to try again. bugfix based on which branch?

---

## Comment 22

> Username: vinniefalco  
> Created at: 2017-06-29 17:54:29 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-312045648  

**develop**

---

## Comment 23

> Username: octopus-prime  
> Created at: 2017-06-29 18:18:56 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-312052342  

Headline for CHANGELOG.md in bugfix?  
Currently it is "Version 70"...

---

## Comment 24

> Username: vinniefalco  
> Created at: 2017-06-29 18:20:14 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-312052683  

```  
Version 71:  
  
--------------------------------------------------------------------------------  
  
Version 70:  
...  
```

---

## Comment 25

> Username: octopus-prime  
> Created at: 2017-06-29 18:21:08 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-312052935  

"Version 71"? I'm asking because there is a v71 branch...

---

## Comment 26

> Username: vinniefalco  
> Created at: 2017-06-29 18:22:50 UTC  
> Url: https://github.com/boostorg/beast/issues/553#issuecomment-312053373  

Its fine, when your pull request passes tests I will just cherry-pick your commit into the **v71** branch.
