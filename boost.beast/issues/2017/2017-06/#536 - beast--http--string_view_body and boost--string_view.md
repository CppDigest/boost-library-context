# #536 - beast::http::string_view_body and boost::string_view [Closed]

> Username: octopus-prime  
> Created at: 2017-06-24 11:36:44 UTC  
> Updated at: 2017-07-04 01:52:44 UTC  
> Closed at: 2017-07-04 01:52:44 UTC  
> Url: https://github.com/boostorg/beast/issues/536  

Using `beast::http::string_view_body` and` boost::string_view`  
```  
void blub()  
{  
    beast::http::request<beast::http::string_view_body> request;  
    request.body = boost::string_view("Hello, world!");  
}  
```  
gives this error:  
```  
gcc.compile.c++ bin/gcc-7/release/example/foo.o  
  
    "g++"  -ftemplate-depth-128 -march=native -ftemplate-depth=256 -std=c++17 -O3 -finline-functions -Wno-inline -Wall -fPIC  -DBOOST_ALL_DYN_LINK -DNDEBUG  -I"/home/mike/workspace/Beast/include" -I"include" -c -o "bin/gcc-7/release/example/foo.o" "example/foo.cpp"  
  
example/foo.cpp: In function ‘void blub()’:  
example/foo.cpp:168:51: error: no match for ‘operator=’ (operand types are ‘beast::http::string_view_body::value_type {aka boost::basic_string_ref<char, std::char_traits<char> >}’ and ‘boost::basic_string_view<char, std::char_traits<char> >’)  
  request.body = boost::string_view("Hello, world!");  
                                                   ^  
In file included from /home/mike/workspace/Beast/include/beast/core/string.hpp:11:0,  
                 from /home/mike/workspace/Beast/include/beast/core/static_string.hpp:12,  
                 from /home/mike/workspace/Beast/include/beast/core.hpp:29,  
                 from /home/mike/workspace/Beast/include/beast.hpp:13,  
                 from example/foo.cpp:8:  
/usr/include/boost/utility/string_ref.hpp:77:27: note: candidate: constexpr boost::basic_string_ref<charT, traits>& boost::basic_string_ref<charT, traits>::operator=(const boost::basic_string_ref<charT, traits>&) [with charT = char; traits = std::char_traits<char>]  
         basic_string_ref& operator=(const basic_string_ref &rhs) BOOST_NOEXCEPT  
                           ^~~~~~~~  
/usr/include/boost/utility/string_ref.hpp:77:27: note:   no known conversion for argument 1 from ‘boost::basic_string_view<char, std::char_traits<char> >’ to ‘const boost::basic_string_ref<char, std::char_traits<char> >&’  
...failed gcc.compile.c++ bin/gcc-7/release/example/foo.o...  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-24 12:33:14 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310835985  

`boost::string_view` and `boost::string_ref` are two different classes. Beast declares `beast::string_view` as an alias for `boost:string_ref`. You need to write:  
```  
void blub()  
{  
    beast::http::request<beast::http::string_view_body> request;  
    request.body = beast::string_view(Hello, world!");  
}  
```

---

## Comment 2

> Username: octopus-prime  
> Created at: 2017-06-24 12:55:36 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310836982  

I know. May be the name `beast::http::string_ref_body` is better.  
- So everybody knows to use `boost:string_ref`.  
- No need for `beast::string_view` as an alias for `boost:string_ref`  
- In future a `beast::http::string_view_body` could work with `boost::string_view`

---

## Comment 3

> Username: octopus-prime  
> Created at: 2017-06-24 13:23:24 UTC  
> Updated at: 2017-06-24 13:33:11 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310838274  

Or even better a generic and configurable design for `beast::http::string_body`:  
```  
template <typename StringRep>  
struct string_body  
{  
    /// The type of the body member when used in a message.  
    using value_type = StringRep;  
  
    ...  
};  
```  
So `StringRep` could be at least  
- `std::string`  
- `boost::string`  
- `std::string_view`  
- `boost::string_view`  
- `boost::string_ref`

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-24 14:03:05 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310840340  

I would use `std::string_view` if it was part of C++11 or if Beast required C++17. But since its not available, and because using non-owning strings in Beast interfaces provides tremendous benefits, I had to pick an implementation of `string_view`. I chose `boost::basic_string_ref` because that's the one I found first. I guess I could have gone with `boost::basic_string_view` although I'm not sure what the differences is, and why does Boost have two of them? This is something that should be brought up during the formal review.  
  
There's no way that Beast can provide every possible body type nor would I want to do so, it just needs to provide the common types. The rest can be left to users - you can write your own **Body** for any class that you desire, its pretty easy:  
http://vinniefalco.github.io/beast/beast/using_http/custom_body_types.html  
http://vinniefalco.github.io/beast/beast/concept/Body.html

---

## Comment 5

> Username: octopus-prime  
> Created at: 2017-06-24 14:34:46 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310842165  

Using the generic `string_body`  
```  
template <typename StringRep>  
struct string_body  
{  
    /// The type of the body member when used in a message.  
    using value_type = StringRep;  
  
    ...  
};  
```  
compiles this without problems out-of-the-box  
```  
template <typename StringRep>  
void test()  
{  
    boost::asio::io_service service;  
    boost::asio::ip::tcp::socket socket {service};  
    beast::http::request<beast::http::string_body<StringRep>> request;  
    request.body = StringRep();  
    beast::http::write(socket, request);  
}  
  
void test()  
{  
    test<std::string>();  
    test<boost::container::string>();  
    test<std::string_view>();  
    test<boost::string_view>();  
    test<boost::string_ref>();  
    test<std::vector<char>>();  
    test<boost::container::vector<char>>();  
    test<std::array<char, 100>>();  
    test<boost::array<char, 100>>();  
}  
```  
That's the generic way. No need to restrict the user at all.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-24 15:00:01 UTC  
> Updated at: 2017-06-24 15:00:18 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310843463  

>`template <typename StringRep>`  
>`struct string_body`  
  
In designing Beast interfaces, I try to keep the types as simple as possible. For example `string_body` is limited to `std::string`, and not `std:basic_string<CharT, Traits, Allocator>`. I don't think I want to make `string_body` a template now since that complicates a very common use case to satisfy the needs of only a small number of users. How many people plan on mixing all the different types of containers you listed in your example, in the same program? And as you correctly pointed out, implementing a **Body** type is trivial.  
  
You left out the part about writing - you can't parse into a `string_view`. Still, you would need a correctly written trait metafunction or else users would get odd compile errors when using `StringRep` that don't meet the requirements (for example `std::list<char>` won't work).  
  
Its not clear to me that Beast benefits from enlarging its scope to include a body for all conceivable containers that have `data()` and `size()` for reading, and `reserve()` and `append()` for writing. However, if you feel strongly about it, then I encourage you to participate in the Boost formal review! Perhaps your idea will gain some traction. Perhaps others will make proposals to improve the idea and change my mind? You never know!  
  
All you have to do is sign up for the mailing list before July 1 and listen for the announcements:  
http://www.boost.org/community/groups.html#main

---

## Comment 7

> Username: octopus-prime  
> Created at: 2017-06-24 15:12:17 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310844153  

> How many people plan on mixing all the different types of containers you listed in your example, in the same program?  
  
Nobody - i think. But  
- People work with existing source code - which specifies the types you must use.  
- People use frameworks - which specifies the types you must use.  
- Be able to use your string_body with any type specified by existing code or framework is a huge gain.

---

## Comment 8

> Username: octopus-prime  
> Created at: 2017-06-24 15:13:22 UTC  
> Updated at: 2017-06-24 15:20:10 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310844237  

```  
void bad1()  
{  
    boost::asio::io_service service;  
    boost::asio::ip::tcp::socket socket {service};  
    beast::http::response<beast::http::string_view_body> response;  
    beast::flat_buffer buffer;  
    beast::http::read(socket, buffer, response);  
}  
```  
is as broken as  
```  
void bad2()  
{  
    boost::asio::io_service service;  
    boost::asio::ip::tcp::socket socket {service};  
    beast::http::response<beast::http::string_body<std::string_view>> response;  
    beast::flat_buffer buffer;  
    beast::http::read(socket, buffer, response);  
}  
```

---

## Comment 9

> Username: octopus-prime  
> Created at: 2017-06-24 15:19:39 UTC  
> Updated at: 2017-06-24 15:21:07 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310844657  

But a nice way could be  
- `mutable_sting_body` for write and read  
- `immutable_sting_body` for write only  
  
`mutable_sting_body` needs mutable strings:  
- `std::string`  
- `boost::container::string`  
- `std::vector<char>`  
  
`immutable_sting_body` can handle immutable strings too  
- see all examples above - including refs and views  
  
That's clear design, isn't it?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-06-24 15:40:23 UTC  
> Updated at: 2017-06-24 15:40:45 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310845973  

I like the names although I would go with `const_string_body` and `mutable_string_body`. Or maybe even `const_body` and `mutable_body`.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-06-24 15:53:08 UTC  
> Updated at: 2017-06-24 15:53:27 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310846818  

>is as broken as  
  
 Yes, `read` on non-writable bodies will produce this error  
```  
1> static assertion failed: BodyWriter requirements not met  
```  
  
But I am referring to the error generated when the user chooses an incorrect type for writing. For example if they try to use `string_body<std::list<char>>` they will see something like this:  
```  
1>C:\Users\vinnie\src\Beast\test\http\read.cpp(80): error C2440: 'initializing': cannot convert from 'initializer list' to 'beast::http::string_bod<beast::multi_buffer>::reader::const_buffers_type'  
1>C:\Users\vinnie\src\Beast\test\http\read.cpp(80): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
1>C:\Users\vinnie\src\Beast\test\http\read.cpp(77): note: while compiling class template member function 'boost::optional<std::pair<beast::http::string_body::reader::const_buffers_type,bool>> beast::http::string_bod<beast::multi_buffer>::reader::get(beast::error_code &)'  
1>C:\Users\vinnie\src\Beast\include\beast/http/impl/serializer.ipp(77): note: see reference to function template instantiation 'boost::optional<std::pair<beast::http::string_body::reader::const_buffers_type,bool>> beast::http::string_bod<beast::multi_buffer>::reader::get(beast::error_code &)' being compiled  
1>C:\Users\vinnie\src\Beast\include\beast/http/impl/write.ipp(738): note: see reference to class template instantiation 'beast::http::string_bod<beast::multi_buffer>::reader' being compiled  
1>C:\Users\vinnie\src\Beast\test\http\read.cpp(205): note: see reference to function template instantiation 'void beast::http::write<beast::test::pipe::stream,false,beast::http::string_bod<beast::multi_buffer>,beast::http::fields>(SyncWriteStream &,const beast::http::message<false,beast::http::string_bod<beast::multi_buffer>,beast::http::fields> &)' being compiled  
1>        with  
1>        [  
1>            SyncWriteStream=beast::test::pipe::stream  
1>        ]  
1>C:\Users\vinnie\src\Beast\test\http\read.cpp(80): error C2440: 'return': cannot convert from 'initializer list' to 'boost::optional<std::pair<beast::http::string_body::reader::const_buffers_type,bool>>'  
1>C:\Users\vinnie\src\Beast\test\http\read.cpp(80): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
```  
  
`string_body` needs a traits metafunction to perform type checking on its template parameter to make sure it meets the requirements, otherwise the user will see an error novel.

---

## Comment 12

> Username: octopus-prime  
> Created at: 2017-06-24 16:05:48 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310847564  

Agreed! The `mutable_sting_body` should check the `StringRep` for method  
  
- append  
- and/or reserve  
  
in a meta `is_mutable<StringRep>::value`

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-06-24 16:35:54 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310849246  

I'm warming up to the idea

---

## Comment 14

> Username: octopus-prime  
> Created at: 2017-06-24 17:53:32 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310853726  

If `xxx_string_body` get a template argument `StringRep`  
there should be a check like  
`static_assert(sizeof(typename StringRep::value_type) == 1, "Unsupported character size.");`  
too. Otherwise you could compile this:  
```  
    test<std::wstring>();  
    test<std::u32string>();  
    test<std::u16string>();  
```  
And this does not work. Adding utf16, utf32, etc. support is far behind this scope...

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-06-24 18:20:38 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310855911  

Ouch, that's a problem... But why not support those as well? The calculation for content length would be `value.size() * sizeof(typename value_type::value_type)`. The container would require a nested type called `value_type`. Which they all have.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2017-06-24 18:23:22 UTC  
> Updated at: 2017-06-24 18:26:25 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310856147  

Although, this brings up a new problem. How do you prevent this  
```  
class Thing {  
  unsigned char v_;  
public:  
  ...  
};  
```  
  
Now use `const_body<std::vector<Thing>>` This doesn't really make any sense because there is no guarantee that `Thing` is something valid to perform a bitwise serialization on.  
  
And what if we have  
```  
struct Thing2 {  
  std::string s_;  
};  
```  
  
Now use `const_body<std::vector<Thing2>>` this is undefined behavior. I am starting not to like this idea.

---

## Comment 17

> Username: octopus-prime  
> Created at: 2017-06-24 18:39:20 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310857858  

`std::is_integral` for `StringRep::value_type` will sort `Thing` and `Thing2` out.

---

## Comment 18

> Username: vinniefalco  
> Created at: 2017-06-24 18:41:40 UTC  
> Updated at: 2017-06-24 18:45:52 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310858150  

On second thought let me think about this some more. Maybe it should wait until after the formal review which starts July 1st.

---

## Comment 19

> Username: octopus-prime  
> Created at: 2017-06-24 18:51:18 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310859007  

okay

---

## Comment 20

> Username: octopus-prime  
> Created at: 2017-06-24 19:07:55 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310859882  

I can do it now in the fork. If you want i can give you a pull request after the formal review.

---

## Comment 21

> Username: vinniefalco  
> Created at: 2017-06-24 19:13:08 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310860152  

I don't want to waste your time, I am trying to get feedback from some other people who have worked on Boost to know if this is a good idea or not. Remember that every additional feature means additional testing. maintenance, and documentation. So it has to really be worth it.

---

## Comment 22

> Username: octopus-prime  
> Created at: 2017-06-24 19:47:42 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310861855  

Roger that

---

## Comment 23

> Username: vinniefalco  
> Created at: 2017-06-25 18:05:34 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310918115  

Okay I have discussed it with my colleagues and come to a conclusion. I have reservations about making `const_body` and `mutable_body` class templates part of the library's public interface. However, having them as example code (which is compiled and tested as part of the continuous integration process) included in the documentation would be very nice. Users can simply copy the source file into their project to use it.  
  
This doesn't stop us from promoting it to become part of the library in the future. This way, it creates less work in terms of maintenance, documentation, and testing, and also serves a purpose, to show users how to create body types.  
  
It would go in `example/common` which is where I am putting other similar things like `file_body.hpp`  
  
What do you think?

---

## Comment 24

> Username: octopus-prime  
> Created at: 2017-06-26 07:52:51 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-310988122  

Sounds good.

---

## Comment 25

> Username: vinniefalco  
> Created at: 2017-07-04 01:52:44 UTC  
> Url: https://github.com/boostorg/beast/issues/536#issuecomment-312761715  

This should all be handled by https://github.com/vinniefalco/Beast/issues/580
