# #360 - Tuple serialization yields incorrect results [Closed]

> Username: anarthal  
> Created at: 2025-11-27 13:56:55 UTC  
> Updated at: 2025-12-02 10:20:02 UTC  
> Closed at: 2025-12-02 10:20:02 UTC  
> Url: https://github.com/boostorg/redis/issues/360  

```cpp  
void test_tuple()  
{  
   std::vector<std::tuple<std::string_view, int, unsigned char>> vec{  
      {"k1", 42, 1}  
   };  
   request req;  
   req.push_range("GET", vec);  
   BOOST_TEST_EQ(req.payload(), "*4\r\n$3\r\nGET\r\n$2\r\nk1\r\n$2\r\n42\r\n$1\r\n1\r\n");  
}  
```  
  
This compiles but produces an invalid RESP3 message:  
  
```  
*2  
$3  
GET  
$2  
k1  
$2  
42  
$1  
1  
```  
  
I think that specializing `bulk_counter` should be enough to fix this.

---

## Comment 1

> Username: D0zee  
> Created at: 2025-11-29 19:30:48 UTC  
> Url: https://github.com/boostorg/redis/issues/360#issuecomment-3591888155  

Hi @anarthal, if you would like I can take this issue. I would be happy to help!

---

## Comment 2

> Username: anarthal  
> Created at: 2025-11-29 19:32:02 UTC  
> Url: https://github.com/boostorg/redis/issues/360#issuecomment-3591888779  

I've actually fixed this already, sorry, just didn't upload the PR yet. But thanks.
