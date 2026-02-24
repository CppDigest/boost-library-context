# #1963 - Overloading async_send for file_body is broken [Closed]

> Username: k0zmo  
> Created at: 2020-05-27 18:03:49 UTC  
> Updated at: 2023-08-30 00:33:04 UTC  
> Closed at: 2020-10-04 03:01:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1963  

Current implementation adds an overload for `boost::beast::http::async_write_some` when the Body type is `basic_file_body<file_win32>`.  
However, this overload is only picked up by MSVC when `/permissive-` flag is not provided - doesn't matter if 2017 or 2019. Compilers that implements two-phase lookup - that is GCC, Clang and MSVC with `/permissive-` flag don't consider this overload.   
  
That is because the [function](https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/impl/write.hpp#L202) that is supposed to call our specialized overload or the default one calls it with the qualified name. This, as opposed to [unqualified name lookup](https://en.cppreference.com/w/cpp/language/unqualified_lookup) makes the ADL to only consider functions that are visible from the template definition context. In the latter case it would also consider functions that are visible from template instantiation context.   
  
Below snippet shows the difference:  
  
```cpp  
namespace abc {  
  
template <typename T>  
struct serializer {};  
  
template <typename Body>  
int async_write_some(serializer<Body>) // primary template  
{  
    return 1;  
}  
  
template <typename Body>  
int async_write(serializer<Body> sr) // 1st variant, fully qualified name lookup  
{  
    return abc::async_write_some(sr);  
}  
  
template <typename Body>  
int async_write2(serializer<Body> sr) // 2nd variant, unqualified name lookup  
{  
    return async_write_some(sr);  
}  
  
struct body_spec {};  
  
int async_write_some(serializer<body_spec>) // our "specialization" for Body=body_spec  
{  
    return 2;  
}  
  
}  
  
int foo()  
{  
    abc::serializer<abc::body_spec> sr;  
    return abc::async_write(sr); // returns 1  
}  
  
int bar()  
{  
    abc::serializer<abc::body_spec> sr;  
    return abc::async_write2(sr); // returns 2  
}  
```  
  
The quickest fix would be to drop name qualification. Another way would be to introduce the dedicated customization point that different bodies could attach to.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-06-01 14:36:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-636896396  

Hi @k0zmo , Do you have some sample code that demonstrates this problem in pure beast terms, perhaps on https://godbolt.org/ ?

---

## Comment 2

> Username: k0zmo  
> Created at: 2020-06-01 18:20:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-637026560  

Beside what I presented I don't have any. I was hoping such distilled snippet would be better than full blown Beast's case. I was trying to add linux_body optimization (`sendfile`) based on #593 and couldn't make it work under GCC and Clang with current machinery.  
  
However, if you need it shown in Beast "realm" simply build any of Beast's examples that use file_body (http/server/fast is my choice) and set a breakpoint [here](https://github.com/boostorg/beast/blob/13a928a872a54f0a90b4a166e0792efeaebf7538/include/boost/beast/http/impl/file_body_win32.hpp#L446). Under MSVC without `/permissive-` flag you should hit the breakpoint during `async_send` when the file is present. With `/permissive-` added this function won't be called and you'll not hit the breakpoint.

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-06-01 18:29:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-637030812  

Awesome, thanks for the pointers

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-06-01 19:38:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-637062116  

That whole `sendfile` thing is shaky

---

## Comment 5

> Username: k0zmo  
> Created at: 2020-06-01 19:44:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-637064791  

As in the whole concept or particular implementation (Linux)?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2020-07-03 03:29:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-653321327  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-07-03 03:54:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-653329352  

I thought this was already fixed in develop?

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-07-03 05:43:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-653362415  

No, I got to the bottom of it and was then distracted with other things.

---

## Comment 9

> Username: ghost  
> Created at: 2020-07-19 08:42:10 UTC  
> Updated at: 2020-07-19 08:43:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-660610258  

@k0zmo  
I think sendfile() optimization is irrelevant in 2020 because everyone and his brother uses TLS.

---

## Comment 10

> Username: k0zmo  
> Created at: 2020-07-19 09:32:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-660616278  

From my experience TLS is not used everywhere - it is used on network boundary (TLS termination) but it's a bit of an overkill inside private network between (micro)services.

---

## Comment 11

> Username: stale[bot]  
> Created at: 2020-08-19 07:31:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-675904285  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-08-19 13:14:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-676337350  

ping

---

## Comment 13

> Username: stale[bot]  
> Created at: 2020-09-20 03:28:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-695613685  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 14

> Username: stale[bot]  
> Created at: 2020-10-04 03:01:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-703193614  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 15

> Username: iamhaiwen  
> Created at: 2023-08-30 00:33:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1963#issuecomment-1698320123  

> 我认为 sendfile（） 优化在 2020 年无关紧要，因为每个人和他的兄弟都使用 TLS。  
  
openssl 3.0 has ssl_sendfile
