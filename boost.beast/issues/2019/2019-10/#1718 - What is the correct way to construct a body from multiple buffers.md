# #1718 - What is the correct way to construct a body from multiple buffers? [Closed]

> Username: smccauliff  
> Created at: 2019-10-04 18:13:29 UTC  
> Updated at: 2019-11-13 14:30:17 UTC  
> Closed at: 2019-11-13 14:30:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1718  

I have bunch of independent pieces of data that I want to place in the body of single http PUT body.  Preferably I would like to do this without making a copy of the data; that is not making a single flat buffer of the data.  Is the following code example the correct way to do this?  
  
```c++  
  std::string a = "abc";  
  std::string b = "wxyz";  
  auto buffer0 = boost::asio::buffer(a);  
  auto buffer1 = boost::asio::buffer(b);  
  std::vector buffers{buffer0, buffer1};  
  auto buffer_adapter = boost::beast::buffers_adaptor(buffers);  
  
  using request_type = boost::beast::http::request<boost::beast::http::basic_dynamic_body<decltype(buffer_adapter)>>;  
  using header_type = request_type::header_type;  
  auto request = std::make_shared<request_type>(header_type(), buffer_adapter);  
  
  request->body() = buffer_adapter;  
  request->method(boost::beast::http::verb::put);  
....  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-04 20:03:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1718#issuecomment-538541538  

if it works, then it is the correct way :)

---

## Comment 2

> Username: smccauliff  
> Created at: 2019-10-07 08:03:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1718#issuecomment-538884501  

So it looks like Body.size returns 0 unless the calls to prepare and commit are made.  Are there any potential other side effects from making these calls (e.g. memory allocation)?  Or do they just change the count of readable bytes?  
  
```c++  
TEST(BoostTest, TestAsioBufferBody) {  
  
  std::string a = "abc";  
  std::string b = "wxyz";  
  auto buffer0 = boost::asio::buffer(a);  
  auto buffer1 = boost::asio::buffer(b);  
  std::vector buffers{buffer0, buffer1};  
  auto buffer_adaptor = boost::beast::buffers_adaptor(buffers);  
  buffer_adaptor.prepare(a.size() + b.size());  
  buffer_adaptor.commit(a.size() + b.size());  
  std::cout << "buffer_adapter.size() " << buffer_adaptor.size() << std::endl;  
  std::cout << "buffer_adapter.max_size() " << buffer_adaptor.max_size() << std::endl;  
  
  using request_type = boost::beast::http::request<boost::beast::http::basic_dynamic_body<decltype(buffer_adaptor)>>;  
  using header_type = request_type::header_type;  
  auto request = std::make_shared<request_type>(header_type(), buffer_adaptor);  
  
  std::cout << "body().size() " << request->body().size() << std::endl;  
  auto data = request->body().data();  
  auto buffer_it = boost::asio::buffer_sequence_begin(data);  
  auto buffer_end = boost::asio::buffer_sequence_end(data);  
  ASSERT_EQ((*buffer_it).size(), 3);  
  ASSERT_EQ('a', static_cast<const char *>((*buffer_it).data())[0]);  
  ASSERT_EQ('b', static_cast<const char *>((*buffer_it).data())[1]);  
  ASSERT_EQ('c', static_cast<const char *>((*buffer_it).data())[2]);  
  buffer_it++;  
  ASSERT_EQ((*buffer_it).size(), 4);  
  ASSERT_EQ('w', static_cast<const char *>((*buffer_it).data())[0]);  
  ASSERT_EQ('x', static_cast<const char *>((*buffer_it).data())[1]);  
  ASSERT_EQ('y', static_cast<const char *>((*buffer_it).data())[2]);  
  ASSERT_EQ('z', static_cast<const char *>((*buffer_it).data())[3]);  
    
}  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-10-07 09:14:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1718#issuecomment-538910943  

All it does is set the Content-Length field for you, which you could do manually if you wanted. There is a memory allocation to store the field/value pair.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-11-06 09:38:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1718#issuecomment-550227994  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-11-13 14:30:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1718#issuecomment-553428343  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
