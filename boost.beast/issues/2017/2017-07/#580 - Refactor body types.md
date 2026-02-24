# #580 - Refactor body types [Closed]

> Username: vinniefalco  
> Created at: 2017-07-03 23:00:08 UTC  
> Updated at: 2017-07-11 00:06:46 UTC  
> Closed at: 2017-07-11 00:06:46 UTC  
> Url: https://github.com/boostorg/beast/issues/580  

```  
template<class T, class Allocator = std::allocator<T>>  
struct vector_body  
{  
    static_assert(  
        std::is_integral<T>::value &&  
        sizeof(T) == 1,  
        "vector_body requirements not met");  
  
    struct reader;  
    struct writer;  
  
    using value_type = std::vector<T, Allocator>;  
};  
  
template<class CharT, class Traits = std::traits<CharT>,  
    class Allocator = std::allocator<CharT>>  
struct basic_string_body  
{  
    static_assert(  
        std::is_integral<CharT>::value &&  
        sizeof(T) == 1,  
        "basic_string_body requirements not met");  
  
    struct reader;  
    struct writer;  
  
    using value_type = std::basic_string<CharT, Traits, Allocator>;  
};  
  
using string_body = basic_string_body<char>;  
  
template<class T>  
struct span_body  
{  
    static_assert(  
        std::is_integral<T>::value &&  
        sizeof(T) == 1,  
        "span_body requirements not met");  
  
    class reader;  
    class writer;  
  
    class value_type  
    {  
        T* data_ = nullptr;  
        std::size_t size_ = 0;  
  
    public:  
        using value_type = typename std::remove_const<T>::type;  
        using pointer = T*;  
        using reference = T&;  
        using iterator = pointer;  
        using const_pointer = T const*;  
        using const_reference = T const&;  
        using const_iterator = const_pointer;  
  
        value_type() = default;  
        value_type(value_type const&) = default;  
        value_type& operator=(value_type const&) = default;  
  
        value_type(T* data, std::size_t size)  
            : data_(data), size_(size)  
        {  
        }  
  
        template<class ContiguousRange>  
        value_type(ContiguousRange const& range);  
  
        template<class ContiguousRange>  
        value_type& operator=(ContiguousRange const& range);  
  
        T* data() const { return data_; }  
        std::size_t size() const { return size_; }  
  
        iterator begin() { return data_; }  
        const_iterator begin() const { return data_; }  
        const_iterator cbegin() const { return data_; }  
  
        iterator end() { return data_ + size_; }  
        const_iterator end() const { return data_ + size_; }  
        const_iterator cend() const { return data_ + size_; }  
    };  
};  
```

---

## Comment 1

> Username: octopus-prime  
> Created at: 2017-07-04 14:40:28 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312894665  

Vinnie, what can i do?! I am baffled...  
  
Okay, i will try again.  
Do you know "Don´t Repeat Yourself (DRY)"?!  
Let's have a look:  
  
string_view_body:  
```  
    class reader  
    {  
        string_view body_;  
  
    public:  
        using const_buffers_type =  
            boost::asio::const_buffers_1;  
  
        template<bool isRequest, class Fields>  
        explicit  
        reader(message<isRequest, string_view_body,  
                Fields> const& m, error_code& ec)  
            : body_(m.body)  
        {  
            ec.assign(0, ec.category());  
        }  
  
        boost::optional<std::pair<const_buffers_type, bool>>  
        get(error_code& ec)  
        {  
            ec.assign(0, ec.category());  
            return {{{body_.data(), body_.size()}, false}};  
        }  
    };  
```  
string_body:  
```  
    class reader  
    {  
        value_type const& body_;  
  
    public:  
        using const_buffers_type =  
            boost::asio::const_buffers_1;  
  
        template<bool isRequest, class Fields>  
        explicit  
        reader(message<isRequest, string_body,  
                Fields> const& msg, error_code& ec)  
            : body_(msg.body)  
        {  
            ec.assign(0, ec.category());  
        }  
  
        boost::optional<std::pair<const_buffers_type, bool>>  
        get(error_code& ec)  
        {  
            ec.assign(0, ec.category());  
            return {{const_buffers_type{  
                body_.data(), body_.size()}, false}};  
        }  
    };  
```  
const_body:  
```  
    class reader  
    {  
        value_type const& body_;  
  
    public:  
        using const_buffers_type =  
            boost::asio::const_buffers_1;  
  
        template<bool isRequest, class Fields>  
        explicit  
        reader(beast::http::message<isRequest, const_body,  
                Fields> const& msg, beast::error_code& ec)  
            : body_(msg.body)  
        {  
            ec.assign(0, ec.category());  
        }  
  
        boost::optional<std::pair<const_buffers_type, bool>>  
        get(beast::error_code& ec)  
        {  
            ec.assign(0, ec.category());  
            return {{const_buffers_type{  
                body_.data(), body_.size()}, false}};  
        }  
    };  
```  
mutable_body:  
```  
    class reader  
    {  
        value_type const& body_;  
  
    public:  
        using const_buffers_type =  
            boost::asio::const_buffers_1;  
  
        template<bool isRequest, class Fields>  
        explicit  
        reader(beast::http::message<isRequest, mutable_body,  
                Fields> const& msg, beast::error_code& ec)  
            : body_(msg.body)  
        {  
            ec.assign(0, ec.category());  
        }  
  
        boost::optional<std::pair<const_buffers_type, bool>>  
        get(beast::error_code& ec)  
        {  
            ec.assign(0, ec.category());  
            return {{const_buffers_type{  
                body_.data(), body_.size()}, false}};  
        }  
    };  
```  
  
Can you see the redundance?? (4x)  
And now you suggest a new body type for `std::vector`.  
This will increase the redundance, since the code will be the same as above (5x).  
  
5x redundant means (at least):  
  
- 5x code change after Reader concept change.  
- 5x bugfixes  
- 5x tests  
- 5x documentation  
  
Why the code is 4-5x redundant?  
Because all these implementations are agains a "character sequence" concept.   
They all rely on `size()` and `data()`  
  
I could show the same for xxx_boxy::size().   
Currently 4x redundant.  
Will be 5x redundant with `vector_body`.  
  
I could partly show the same for xxx_body::writer classes.  
Currently 2x redundant.  
Will be 3x redundant with `vector_body`.  
  
**That's not DRY!!**  
  
Do you remember my notice at review?  
  
```  
Instead of having more and more body types tightly bounded to distinct  
types i would prefer less body types loosely bounded to concepts.  
  
So with a concept of "character sequence" we could introduce a  
"sequence_body" working with models like std::string, std::string_view,  
boost::string_ref, std::vector<char>, etc.   
  
...  
  
So we end up with less body types and less redundance, but much more  
flexibility and testability.  
```  
  
So the goal should be to replace all these bodies by ONE gerenic body, e.g. sequence_body.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-04 14:50:28 UTC  
> Updated at: 2017-07-04 14:52:24 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312896932  

>So the goal should be to replace all these bodies by ONE gerenic body, e.g. sequence_body.  
  
Well yeah, the point is that this replaces `const_body` and `mutable_body`:  
  
* `span_body` replaces `const_body`  
  
* `vector_body` and `string_body` replace `mutable_body`  
  
Notice how `span_body` may be constructed or assigned from **any ContiguousSequence**, this means it replaces any use of `const_body` without knowing the type of the container.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-04 14:53:42 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312897653  

Which part is confusing?

---

## Comment 4

> Username: MCMicS  
> Created at: 2017-07-04 14:54:56 UTC  
> Updated at: 2017-07-04 14:55:52 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312897924  

what will be replaced?  
Will the redundant ways remove?  
What will still exists?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-04 15:01:35 UTC  
> Updated at: 2017-07-04 15:02:06 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312899291  

These files are removed:  
`"example/common/const_body.hpp"`  
`"example/common/mutable_body.hpp"`  
`<beast/http/string_view_body.hpp>`  
  
These files are added:  
`<beast/http/span_body.hpp>`  
`<beast/http/vector_body.hpp>`  
  
>what will replaced?  
  
`span_body` replaces `const_body`  
`string_body` and `vector_body` replace `mutable_body`  
  
>Will the redundant ways remove?  
  
Yes, although `string_body` and `vector_body` will have implementations which look identical. This is an acceptable tradeoff because there is no standard concept which describes a container that has "append" and "reserve". I prefer not to invent new container concepts for this, so we can live with having both `string_body` and `vector_body`. It also looks better at call sites than `mutable_body<std::vector<char>>`  
  
>What will still exists?  
  
`string_body` will still exist.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-04 15:04:11 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312899812  

Under this new scheme, this code would work:  
```  
request<span_body<char>> req;  
  
std::array<char, 32> a;  
std::vector<char> v;  
std::string<char> s;  
string_view sv;  
  
req.body = a;  
req.body = v;  
req.body = s;  
req.body = sv;  
```  
  
Note how all the different containers can be assigned to `span_body<char>` even though the type of container is not mentioned in the `span_body` template argument list.

---

## Comment 7

> Username: octopus-prime  
> Created at: 2017-07-04 15:12:23 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312901465  

> standard concept which describes a container that has "append" and "reserve".  
  
Why we need a **standard** concept here?!  
These are no standard concepts too:  
  
```  
is_body_reader  
is_body_writer  
is_buffer_sequence  
is_dynamic_buffer  
...  
```  
  
So why we can not define  
```  
is_input_sequence  
is_output_sequence  
```  
or  
```  
is_const_sequence  
is_mutable_sequence  
```  
and use them in TTI for  
```  
sequence_body::reader  
sequence_body::writer  
```  
??

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-07-04 15:15:04 UTC  
> Updated at: 2017-07-04 15:16:53 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312902002  

> `is_input_sequence`  
  
This is a already standard concept already called **ContiguousRange** and it will be part of the refactoring.  
  
> `is_output_sequence`  
  
This is not a standard concept. In general I would rather not invent new concepts if I can avoid it, since it has to be documented and explained to users. It looks like `std::string` and `std::vector` cover the vast, vast majority of use cases. Which containers besides `std::string` and `std::vector` do you think are important to justify creating a new concept and generic code?

---

## Comment 9

> Username: octopus-prime  
> Created at: 2017-07-04 15:21:51 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312903445  

You have to maintain 3x code (string, vector, span) bodies.  
You have to write 3x tests.  
You have to write 3x documentation.  
You have to fix bugs 3x.  
...  
  
It's not the question for more containers (oh no - not more bodies).  
It's a question for having 1 sequence_body instead of 3 (string, vector, span) bodies.  
  
So we end up with LESS documentation. LESS tests. LESS maintainance. LESS ...

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-07-04 15:40:50 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312907294  

DRY is a guideline, not a rule, and it applies to the end user, not to the library writer.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-07-04 15:44:00 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312907911  

>It's a question for having 1 sequence_body instead of 3 (string, vector, span) bodies.  
  
The main advantage of `span_body` is that you don't need to write out the sequence name. You just write `span_body<char>` for example and it works with all of the containers. You haven't acknowledged that.  
  
I don't know what `sequence_body` is, that's new.

---

## Comment 12

> Username: octopus-prime  
> Created at: 2017-07-04 15:46:25 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312908377  

How can i read from stream using a span_body<char>?  
Reading to nullptr?

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-07-04 15:51:45 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312909328  

>How can i read from stream using a span_body?  
```  
std::vector<char> v;  
v.resize(2000);  
  
request<span_body<char>> req{v};  
read(stream, buffer, req);  
```  
  
Alternatively you can write  
```  
request<vector_body<char>> req;  
read(stream, buffer, req);  
```  
  
>Reading to nullptr?  
  
Default constructed spans have a zero length so attempting to read a body into one will produce a "buffer overflow" error just as if you tried to read too many bytes into a dynamic buffer with a size limit.

---

## Comment 14

> Username: octopus-prime  
> Created at: 2017-07-04 15:53:13 UTC  
> Updated at: 2017-07-04 15:56:32 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312909597  

> I don't know what sequence_body is, that's new.  
  
With `sequence_body` we don't need `mutable_body` and `const_body`.  
  
You can write if your sequence matches `is_input_sequence`.  
You can read if your sequence matches `is_output_sequence`.  
  
That's it.

---

## Comment 15

> Username: octopus-prime  
> Created at: 2017-07-04 15:56:04 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312910153  

And with `span_body` i get "buffer overflow" error, if the payload is bigger than `span.size()`. No `resize()` or `append()`, etc...  
  
Got it...

---

## Comment 16

> Username: octopus-prime  
> Created at: 2017-07-04 15:57:07 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312910339  

> You are proposing  
```  
template<class Sequence>  
struct sequence_body;  
```  
?  
  
Yes! Exactly!!

---

## Comment 17

> Username: vinniefalco  
> Created at: 2017-07-04 15:57:13 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312910360  

>And with span_body i get "buffer overflow" error, if the payload is bigger than span.size(). No resize() or append(), etc...  
  
`span_body` represents a fixed size buffer. If you don't want a fixed size buffer, don't use `span_body`. You can use `string_body` or `vector_body`. Or a `dynamic_body`.  
  
But if you want a fixed size non-owning buffer for writing, then `span_body` will work

---

## Comment 18

> Username: vinniefalco  
> Created at: 2017-07-04 16:04:24 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312911640  

>Yes! Exactly!!  
  
`span_body` is strictly superior to `sequence_body` because one instantiation for a given `T` (`char` for example) works for any **ContiguousContainer**. And it does not require spelling the type of container. It is simply the best design for reading from a non-owning, contiguous buffer.  
  
`span_body` also supports writing to a non-owning, contiguous buffer, which `sequence_body` does not.  
  
`span_body` does **not** support writing to a resizable, contiguous buffer. However, these use-cases are covered by `string_body` and `vector_body`. I realize that this involves some code duplication but that is an implementation detail (and one that I think is the right choice).  
  
So your position (correct me if wrong) is that `sequence_body` has the advantage only for the case of writing to a resizable, contiguous buffer that is not a `std::string` or `std::vector`. I don't think its worth inventing a new concept **ResizableContiguousBuffer**, adding it to the documentation, adding tests, just for some hypothetical container that is not `std::string` or `std::vector` which you have not yet given me the name of.  
  
Even so, `string_body` and `vector_body` handle the vast, vast majority of user's needs for a resizable, contiguous buffer body; if that hypothetical container which is not `std::string` or `std::vector` ever does appear I am okay with requiring that it also needs to have its own Body type.

---

## Comment 19

> Username: octopus-prime  
> Created at: 2017-07-04 16:46:36 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312917993  

> `span_body` also supports writing to a non-owning, contiguous buffer, which `sequence_body` does not.  
  
Do you have an example?

---

## Comment 20

> Username: vinniefalco  
> Created at: 2017-07-04 18:17:46 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-312930846  

>Do you have an example?  
  
Yes, I already posted such an example above:  
```  
std::vector<char> v;  
v.resize(2000);  
  
request<span_body<char>> req{v};  
read(stream, buffer, req);  
```

---

## Comment 21

> Username: niklas88  
> Created at: 2017-07-07 08:30:11 UTC  
> Updated at: 2017-07-07 08:32:13 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-313621288  

I think what @octopus-prime is really proposing is having just span_body for everything that is non-resizable and sequence_body for string and vector. I'm with octpus-prime in that the latter would be nicer as a generic template which accepts both std::string and std::vector instead of two separate implementations. Also I feel like file_body.hpp in the examples is also an example for excessive growth, it was less than 100 lines a c couple of weeks ago and now I'm trying to merge a new Beast version and it's at almost 400 lines of much much more complicated code than before in the name of better best practices..   
I get it the new version has nice concepts and seperation but it's vastly over complicated especially for an example that's about shoving bytes from a file into an HTTP request/response.

---

## Comment 22

> Username: vinniefalco  
> Created at: 2017-07-07 12:31:04 UTC  
> Updated at: 2017-07-07 12:32:07 UTC  
> Url: https://github.com/boostorg/beast/issues/580#issuecomment-313668283  

>I'm with octpus-prime in that the latter would be nicer as a generic template  
  
Sorry, I disagree. `string_body` and `vector_body` are better than inventing a new concept.  
  
>... in the name of better best practices  
  
`file_body` is becoming a public interface!  
  
>it's vastly over complicated especially for an example that's about shoving bytes from a file into an HTTP request/response.  
  
Since its going to be public it has to.. follow best practices as you said. The previous version was defective and had a race condition, plus it encouraged sub-optimal usage (it opened the file up to 3 times) just to send one message.  
  
>I get it the new version has nice concepts and seperation but...  
  
Actually the changes to `file_body` are preparation for a **very** good feature, Beast will take advantage of optimized platform-specific interfaces such as `::sendfile` (Linux) and `::TransmitFile` (Windows) when sending file contents!
