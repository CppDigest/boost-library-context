# #1423 - ZeroCopyOutputStream and flat_buffer.. [Closed]

> Username: byronfa  
> Created at: 2019-02-01 21:36:21 UTC  
> Updated at: 2019-02-02 00:23:51 UTC  
> Closed at: 2019-02-01 23:42:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1423  

A question on a closed item from some months ago if somebody happens to know.  
  
https://github.com/boostorg/beast/issues/1180#issuecomment-402920428  
  
Trying the code from this issue I get warnings about a missing_const_iterator on 'boost::asio::mutable_buffer.  I was calling it via:  
  
`            ZeroCopyOutputStream<beast::flat_buffer> zcopy(outputbuffer_, 8192);`  
  
I can't spot any iterators on flatbuffer.  Anybody know what I'm missing?  (using #define BOOST_BEAST_VERSION 181)  
  
Cheers and thanks

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-01 22:08:16 UTC  
> Updated at: 2019-02-01 22:08:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1423#issuecomment-459884759  

Whoops... that `ZeroCopyOutputStream` is from before Asio made the changes to the buffer sequences.   
  
Try using this instead:  
  
```  
using iterator = buffers_iterator_type<buffers_type>;  
```  
  
The metafunction `buffers_iterator_type` is here:  
https://github.com/boostorg/beast/blob/5361224a7d4c3b318fc92616eff23442851bdc6a/include/boost/beast/core/buffer_traits.hpp#L150

---

## Comment 2

> Username: byronfa  
> Created at: 2019-02-01 23:42:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1423#issuecomment-459906142  

Thanks @vinniefalco.  That got me headed in the right direction.    
  
For anybody that happens upon this comment I ended up with this.  (Slight modification to begin() and end() as well to get it to build.  
  
  
```  
/** Implements ZeroCopyOutputStream around a Streambuf.  
    Streambuf matches the public interface defined by boost::asio::streambuf.  
    @tparam Streambuf A type meeting the requirements of Streambuf.  
*/  
template <class Streambuf>  
class ZeroCopyOutputStream  
    : public ::google::protobuf::io::ZeroCopyOutputStream  
{  
private:  
    using buffers_type = typename Streambuf::mutable_buffers_type;  
    using iterator = boost::beast::buffers_iterator_type<buffers_type>;  
    using mutable_buffer = boost::asio::mutable_buffer;  
  
    Streambuf& streambuf_;  
    std::size_t blockSize_;  
    google::protobuf::int64 count_ = 0;  
    std::size_t commit_ = 0;  
    buffers_type buffers_;  
    iterator pos_;  
  
public:  
    explicit  
        ZeroCopyOutputStream(Streambuf& streambuf,  
            std::size_t blockSize);  
  
    ~ZeroCopyOutputStream();  
  
    bool  
        Next(void** data, int* size) override;  
  
    void  
        BackUp(int count) override;  
  
    google::protobuf::int64  
        ByteCount() const override  
    {  
        return count_;  
    }  
};  
  
//------------------------------------------------------------------------------  
  
template <class Streambuf>  
ZeroCopyOutputStream<Streambuf>::ZeroCopyOutputStream(  
    Streambuf& streambuf, std::size_t blockSize)  
    : streambuf_(streambuf)  
    , blockSize_(blockSize)  
    , buffers_(streambuf_.prepare(blockSize_))  
    , pos_(net::buffer_sequence_begin(buffers_))  
{  
}  
  
template <class Streambuf>  
ZeroCopyOutputStream<Streambuf>::~ZeroCopyOutputStream()  
{  
    if (commit_ != 0)  
        streambuf_.commit(commit_);  
}  
  
template <class Streambuf>  
bool  
ZeroCopyOutputStream<Streambuf>::Next(  
    void** data, int* size)  
{  
    if (commit_ != 0)  
    {  
        streambuf_.commit(commit_);  
        count_ += commit_;  
    }  
  
    if (pos_ == net::buffer_sequence_end(buffers_))  
    {  
        buffers_ = streambuf_.prepare(blockSize_);  
        pos_ = net::buffer_sequence_begin(buffers_);  
    }  
  
    *data = boost::asio::buffer_cast<void*>(*pos_);  
    *size = static_cast<int>(boost::asio::buffer_size(*pos_));  
    commit_ = *size;  
    ++pos_;  
    return true;  
}  
  
template <class Streambuf>  
void  
ZeroCopyOutputStream<Streambuf>::BackUp(int count)  
{  
    assert(count <= commit_);  
    auto const n = commit_ - count;  
    streambuf_.commit(n);  
    count_ += n;  
    commit_ = 0;  
}  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-02 00:23:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1423#issuecomment-459912960  

I would rename:  
  
* `Streambuf` to `DynamicBuffer`  
* `streambuf_` to `buffer_`  
  
Also, `buffer_cast` and `buffer_size` are deprecated. I would write:  
```  
net::mutable_buffer b = *pos_;  
```  
And then you can call `b.data()` and `b.size()` instead.
