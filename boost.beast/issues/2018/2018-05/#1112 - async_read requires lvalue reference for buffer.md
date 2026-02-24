# #1112 - async_read requires lvalue reference for buffer [Closed]

> Username: ricejasonf  
> Created at: 2018-05-02 02:25:18 UTC  
> Updated at: 2023-10-07 17:59:07 UTC  
> Closed at: 2019-01-13 02:58:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1112  

Is there a reason `async_read` requires an lvalue reference of a DynamicBuffer?  
  
It is inconsistent with the interface in Asio.  
  
Here is a minimal use case to justify allowing reference wrapped objects:  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
#include <string>  
  
int main() {  
    namespace asio  = boost::asio;  
    namespace beast = boost::beast;  
    using asio::ip::tcp;  
  
    asio::io_service io;  
    beast::websocket::stream<tcp::socket> socket(io);  
    std::string buffer{};  
  
    socket.async_read(  
        asio::dynamic_buffer(buffer),  
        [](auto) { }  
    );  
  
    io.run();  
}  
```  
  
I also found this, but it didn't really address the issue: https://github.com/boostorg/beast/issues/1104  
  
If it isn't by design, I could take a whack at it but not until after C++Now.  
  
Thank you for your consideration.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-02 03:23:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-385854181  

So yeah...this is an issue. I independently came up with the concept of **DynamicBuffer**, modeling Beast's instances of dynamic buffer after the pre-Net.TS-flavored `boost::asio::basic_streambuf`. In that model, the caller is responsible for maintaining the lifetime of the dynamic buffer object, and those objects are passed by lvalue reference. This is consistent with Asio before and after:  
  
Pre-Net.TS-flavored Asio:  
https://www.boost.org/doc/libs/1_65_1/doc/html/boost_asio/reference/async_read_until/overload1.html  
  
Net.TS-flavored Asio:  
https://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/async_read_until/overload5.html  
  
If you look closely, you can see that Asio maintained backward compatibility by keeping the overload which takes the `basic_streambuf` by lvalue reference. I didn't know about the `boost::asio::dynamic_buffer` function at the time that I finished Beast, nor did I realize that some of the newer Asio interfaces were changed to take an rvalue reference (and also take ownership of the dynamic buffer):  
https://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/async_read_until/overload1.html  
  
As you have discovered,  these two approaches are incompatible. And it is not entirely clear how to resolve it. The Net.TS specification for **DynamicBuffer** requires that they are **MoveConstructible** but this eliminates a whole category of dynamic buffer implementations. Specifically, the ones that have inline storage, such as this one found in Beast:  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__static_buffer.html  
  
None of the solutions are particularly good, as they require having two separate dynamic buffer concepts which differ only in the requirement of being move constructible. Creating and documenting the overloads of initiating functions intending to support both of these types of buffers (which is what you're implicitly asking for) is difficult and very likely to confuse users.  
  
You could create a wrapper which contains as a data member, an instance of `boost::asio::dynamic_string_buffer` (which is what you're getting from the return value of `asio::dynamic_buffer(buffer)`. This wrapper would need to meet the requirements of **DynamicBuffer** which is fairly trivial since they would be one line functions that just forward the call into the data member. You would need to put an instance of this wrapper on the stack of `main` or as a global variable so that you could pass it as an lvalue reference.  
  
If you write this, I could add it to `<boost/beast/experimental/core/...>`, a new directory which appears in versions 170 and later and will go into Boost 1.68. Or I could get it to it at some point.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2018-05-02 03:53:07 UTC  
> Updated at: 2018-05-02 03:53:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-385857519  

The only purpose of `asio::dynamic_string_buffer` is to reference wrap a string as a DynamicBuffer.  
  
As a workaround it would be better to make an equivalent wrapper that owns the object and bypass the reference wrapper altogether. (I'd rather it support both which I think would be trivial to implement)  
  
I don't understand the Net-TS's need for the buffer to be MoveConstructible, but for your nonmovable buffers, you could use a reference wrapper similar to the one I am trying to use.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2018-05-02 04:14:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-385859883  

Here is my lame workaround for the time being. :sweat_smile:   
https://github.com/ricejasonf/cppnow18_full_duplex/blob/master/include/beast_ws.hpp#L88

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-05-02 04:53:28 UTC  
> Updated at: 2018-05-02 04:54:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-385864616  

>I don't understand the Net-TS's need for the buffer to be MoveConstructible  
  
The move construction requirement is so that stream algorithms like `boost::asio::async_read_until` can take ownership of the dynamic buffer, as this implementation illustrates:  
https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/impl/read_until.hpp#L55  
  
> Here is my lame workaround for the time being.  
  
An alternative is to use a `beast::flat_buffer` and then just construct a `string_view` out of it:  
  
```  
boost::beast::flat_buffer b;  
ws.read(b);  
std::string_view s{  
    boost::beast::buffers_front(b.data()).data(),  
    boost::beast::buffers_front(b.data()).size()};  
```  
  
My beef with `std::string` is that `basic_string::resize` will default-initialize the new memory, which is unnecessary.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-05-02 04:55:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-385864884  

>I'd rather it support both which I think would be trivial to implement  
  
It might be trivial to implement but it is far from trivial to document and explain to users. We would need two different concepts: **LValue DynamicBuffer** and **RValue DynamicBuffer** (names are for illustration only). This would likely cause endless confusion.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-05-09 01:43:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-387594143  

I have identified more design problems with the Net.TS specification of DynamicBuffer. Specifically that algorithms which accept dynamic buffer parameters cannot be composed without performance penalties. I am exploring solutions.

---

## Comment 7

> Username: ricejasonf  
> Created at: 2018-05-09 02:26:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-387600518  

Are you saying that the performance issues arise from the use of reference wrapper like objects? I would think that it would be the same as a raw lvalue reference.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-05-09 03:15:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-387607527  

I'm saying that if `f` and `g` are asynchronous composed operations which operate on a **DynamicBuffer**, and `f` is implemented in terms of `g`, then due to the semantics of the **DynamicBuffer** concept, `f` must dynamically allocate an additional piece of memory to hold its decay-copied dynamic buffer. Consider what happens otherwise:  
  
1. `f` makes a decay-copy of the dynamic buffer via perfect forwarding  
2. `f` invokes the initiating function for `g`, passing its copy of the dynamic buffer  
3. `g` mutates the dynamic buffer, and control returns to `f`  
4. At this point, the dynamic buffer instance held by `f` is in the state prior to the call to `g`, which is out of sync  
  
In order for `f` to be expressible in terms of `g`, the dynamic buffer it passes to `g` must have these properties:  
  
* Copyable  
* Reference semantics  
* State shared with all copies  
  
This suggests a class containing a `shared_ptr` equivalent, which requires a memory allocation. There is no solution to this problem which does not involve an additional allocation.  
  
Note that if `g` is a composed operation that is itself expressed in terms of some other operation `u` which also accepts a dynamic buffer, then two memory allocations are required. Each additional layer of composition necessitates an additional memory allocation.  
  
It is my position that the semantics of **DynamicBuffer** in networking.ts is flawed, and I will file a defect report.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-05-18 23:41:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-390358958  

See https://rawgit.com/vinniefalco/cpp/5/n4695_issue_5.html

---

## Comment 10

> Username: ricejasonf  
> Created at: 2018-05-19 00:12:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-390362767  

The problem with restricting the interface to using lvalue reference types only is demonstrated in my original minimal use case. Container types that do not satisfy `DynamicBuffer` are difficult to lift without requiring ownership within a dummy wrapper.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-05-19 00:20:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-390363565  

> Container types that do not satisfy DynamicBuffer are difficult to lift without requiring ownership within a dummy wrapper.  
  
My proposal changes the semantics and interface of `dynamic_string_buffer` (and `dynamic_vector_buffer`). Here is your example with the changes:  
  
```  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
#include <string>  
  
int main() {  
    namespace asio  = boost::asio;  
    namespace beast = boost::beast;  
    using asio::ip::tcp;  
  
    asio::io_service io;  
    beast::websocket::stream<tcp::socket> socket(io);  
    asio::dynamic_string_buffer buffer{};  
  
    socket.async_read(  
        buffer,  
        [](auto) { }  
    );  
    io.run();  
  
    std::cout << buffer.get(); // returns a string_view  
    std::string s = buffer.release(); // take ownership via move  
}  
```

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-05-19 00:21:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-390363729  

> The problem with restricting the interface to using lvalue reference types  
  
I am **very** open to hearing alternative solutions. Fair warning though, we have spent considerable time exploring this problem and the alternatives are objectively worse. But there's always a chance that someone will come up with something I haven't thought of.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2018-05-19 00:23:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-390363964  

> The only purpose of asio::dynamic_string_buffer is to reference wrap a string as a DynamicBuffer.  
  
This is technically not correct. `dynamic_string_buffer`, in addition to referencing a string, also maintains metadata about the dynamic buffer. Specifically the `size_` and `max_size_` data members which among other things help define the boundary between the input and output sequences.  
  
In the issue I wrote up, you can see how the presence of this metadata interferes with composition of operations with accept instances of dynamic buffers. So as written the TS **DynamicBuffer** concept is neither a pure reference type, nor is it a pure container type.

---

## Comment 14

> Username: ricejasonf  
> Created at: 2018-05-19 00:38:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-390365245  

> This is technically not correct. dynamic_string_buffer, in addition to referencing a string, also maintains metadata about the dynamic buffer. Specifically the size_ and max_size_ data members which among other things help define the boundary between the input and output sequences.  
  
Ahh! I guess I should have looked at it closer.  
  
It seems wasteful to store those in cases where the user does not specify a maximum. It's like it's there for idiot proofing or something (or I am naive about more stuff).

---

## Comment 15

> Username: vinniefalco  
> Created at: 2018-05-19 01:00:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-390366904  

This is the thinking behind DynamicBuffer:  
  
_"...the right approach is to decouple the backing storage from the view that determines how it is populated/drained (i.e. what Asio::DynamicBuffer represents now). In a nutshell (because there's a lot more to it than I can do justice to right now), as a user I want to keep the same storage across the lifetime of a connection, but vary how it is used: during connection establishment when the peer is untrusted, keep the maximum size small; once trust is established and exchanging larger messages, user a larger maximum; access the storage as a static buffer when forwarding data to multiple destinations. This is, I think, what some users were (implicitly) telling me for years when asking for read_until to work with std::string: it's only a DynamicBuffer when used in the context of the read_until operation; when accessing the storage from other parts of the application they need it to be something else, and they don't want to have to copy the data to get it in the form they require."_  
  
However, it created new unanticipated problems which in my opinion are worse than the problems the original design intends to solve.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2019-01-13 02:58:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-453797744  

Well there is nothing to do here but wait until the paper I have submitted winds its way through wg21 which could be a while since networking is pushed back to 2023. So I will go ahead and close this.

---

## Comment 17

> Username: ecorm  
> Created at: 2023-09-20 03:12:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-1726818361  

I'm currently vexed with this problem. My library takes a `std::vector<uint8_t>` as a message receive destination that I pass on to `websocket::stream::async_read` under the hood, and I want to avoid copying, heap allocations, or exposing Boost buffer types to the user.  
  
I can't use a bare `boost::asio::dynamic_vector_buffer` as a class member, because it's not default constructible. I can't construct it with a dummy `std::vector` either, because it is not assignable.  
  
I could wrap the `boost::asio::dynamic_vector_buffer` in `std::optional`, but that would bump up my library's C++ requirements from C++11. `boost::optional` is not appealing either, because I would like my library to support standalone Asio when Websocket support is not needed.  
  
I'm now left with writing my own default-constructible `std::vector<uint8_t>` wrapper which meets Beast's `DynamicBuffer` requirements. I hope this isn't too hard.

---

## Comment 18

> Username: ecorm  
> Created at: 2023-09-20 04:46:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-1726960940  

I used `boost::asio::dynamic_vector_buffer` as a starter and came up with the code below. It's not clear whether Beast supports Asio's `DynamicBuffer_v2` type requirement, so I went with the v1 requirement.  
  
```c++  
template <typename TStorage>  
class DynamicWebsocketBuffer  
{  
private:  
    static constexpr auto sizeLimit_ = std::numeric_limits<std::size_t>::max();  
  
public:  
    using storage_type = TStorage;  
    using const_buffers_type = boost::asio::const_buffer;  
    using mutable_buffers_type = boost::asio::mutable_buffer;  
  
    DynamicWebsocketBuffer() = default;  
  
    DynamicWebsocketBuffer(TStorage& s, std::size_t maxSize = sizeLimit_)  
        : storage_(&s),  
          maxSize_(maxSize)  
    {}  
  
    std::size_t size() const noexcept  
    {  
        if (!storage_)  
            return 0;  
        return (size_ == noSize_) ? std::min(storage_->size(), max_size())  
                                  : size_;  
    }  
  
    std::size_t max_size() const noexcept {return maxSize_;}  
  
    std::size_t capacity() const noexcept  
    {  
        return storage_ ? std::min(storage_->capacity(), max_size()) : 0;  
    }  
  
    const_buffers_type data() const noexcept  
    {  
        if (storage_)  
            return const_buffers_type{storage_->data(), storage_->size()};  
        else  
            return const_buffers_type{nullptr, 0};  
    }  
  
    mutable_buffers_type prepare(std::size_t n)  
    {  
        assert(storage_ != nullptr);  
        auto s = size();  
        auto m = max_size();  
        if ((s > m) || (m - s < n))  
        {  
            throw std::length_error("DynamicWebsocketBuffer::prepare: "  
                                    "buffer too long");  
        }  
  
        if (size_ == noSize_)  
            size_ = storage_->size();  
        storage_->resize(s + n);  
        return boost::asio::buffer(boost::asio::buffer(*storage_) + size_, n);  
    }  
  
    void commit(std::size_t n)  
    {  
        assert(storage_ != nullptr);  
        size_ += std::min(n, storage_->size() - size_);  
        storage_->resize(size_);  
    }  
  
    void consume(std::size_t n)  
    {  
        assert(storage_ != nullptr);  
        if (size_ != noSize_)  
        {  
            std::size_t length = std::min(n, size_);  
            storage_->erase(storage_->begin(), storage_->begin() + length);  
            size_ -= length;  
            return;  
        }  
        storage_->erase(storage_->begin(),  
                        storage_->begin() + std::min(size(), n));  
    }  
  
    storage_type& storage()  
    {  
        assert(storage_ != nullptr);  
        return *storage_;  
    }  
  
    void reset() {resetStorage(nullptr);}  
  
    void reset(storage_type& storage, std::size_t maxSize = sizeLimit_)  
    {  
        resetStorage(&storage, maxSize);  
    }  
  
private:  
    static constexpr auto noSize_ = std::numeric_limits<std::size_t>::max();  
  
    void resetStorage(storage_type* storage, std::size_t maxSize)  
    {  
        storage_ = storage;  
        size_ = noSize_;  
        maxSize_ = maxSize;  
    }  
  
    TStorage* storage_ = nullptr;  
    std::size_t size_ = noSize_;  
    std::size_t maxSize_ = sizeLimit_;  
};  
```

---

## Comment 19

> Username: ecorm  
> Created at: 2023-10-07 17:59:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1112#issuecomment-1751782675  

> boost::optional is not appealing either, because I would like my library to support standalone Asio when Websocket support is not needed.  
  
I realized that Boost.Beast has a hard dependency on the rest of Boost anyway, so it ended up using a `boost::optional<boost::asio::dynamic_vector_buffer<E,A>>` class member. It can be reset to wrap a new `vector` buffer via the `emplace` method.
