# #1180 - which buffer should i use? [Closed]

> Username: opengpu  
> Created at: 2018-07-06 03:26:49 UTC  
> Updated at: 2018-08-22 21:04:25 UTC  
> Closed at: 2018-08-22 21:04:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1180  

//above is serve code  
beast::flat_buffer flatBuf;  
mutable_buffer mutableBuf = flatBuf.prepare(iSize);  
if (protobuf.ParseFromArray(mutableBuf.data(), iSize)) //ParseFromArray(void *, int)  
{  
    ws_.async_write(flatBuf.data(), ...);  
}  
/////////////////////////////////////////  
//below is client code  
beast::flat_buffer flatBuf;  
ws_async_read(flatBuf);  
The problem is the client recieved data is empty...what buffer should i use at the server code? Thank you!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-06 04:12:14 UTC  
> Updated at: 2018-07-06 04:12:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1180#issuecomment-402920428  

you need to call `flatBuf.commit` with the number of bytes written by `ParseFromArray`. However, if you are using Google Protocol Buffers, there is an even better solution:  
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
    using iterator = typename buffers_type::const_iterator;  
    using mutable_buffer = boost::asio::mutable_buffer;  
  
    Streambuf& streambuf_;  
    std::size_t blockSize_;  
    google::protobuf::int64 count_ = 0;  
    std::size_t commit_ = 0;  
    buffers_type buffers_;  
    iterator pos_;  
  
public:  
    explicit  
    ZeroCopyOutputStream (Streambuf& streambuf,  
        std::size_t blockSize);  
  
    ~ZeroCopyOutputStream();  
  
    bool  
    Next (void** data, int* size) override;  
  
    void  
    BackUp (int count) override;  
  
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
    : streambuf_ (streambuf)  
    , blockSize_ (blockSize)  
    , buffers_ (streambuf_.prepare(blockSize_))  
    , pos_ (buffers_.begin())  
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
  
    if (pos_ == buffers_.end())  
    {  
        buffers_ = streambuf_.prepare (blockSize_);  
        pos_ = buffers_.begin();  
    }  
  
    *data = boost::asio::buffer_cast<void*>(*pos_);  
    *size = boost::asio::buffer_size(*pos_);  
    commit_ = *size;  
    ++pos_;  
    return true;  
}  
  
template <class Streambuf>  
void  
ZeroCopyOutputStream<Streambuf>::BackUp (int count)  
{  
    assert(count <= commit_);  
    auto const n = commit_ - count;  
    streambuf_.commit(n);  
    count_ += n;  
    commit_ = 0;  
}  
```  
  
Use the "zero copy output" interface of `protobuf`, and use `beast::flat_buffer` for `Streambuf`.

---

## Comment 2

> Username: opengpu  
> Created at: 2018-07-06 09:58:56 UTC  
> Updated at: 2018-07-06 09:59:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1180#issuecomment-402988622  

thank you. i will have a try.  
however i use protobuf.js i client...and as i am newbie to js. now i just use proto.decode(msg).finish(). and set type "arraybuffer"......

---

## Comment 3

> Username: xemasiv  
> Created at: 2018-07-16 19:34:39 UTC  
> Updated at: 2018-07-16 19:35:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1180#issuecomment-405356198  

hi @opengpu you might want to try pbf too it is also good alternative for working with protocolbuffers https://www.npmjs.com/package/pbf it's used by mapbox and i've seen benchmarks where it's fast

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-08-15 20:23:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1180#issuecomment-413323678  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-08-22 21:04:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1180#issuecomment-415179388  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
