# #717 - bug on solaris [Closed]

> Username: 1NF1N1TTY  
> Created at: 2022-06-09 09:51:45 UTC  
> Updated at: 2022-06-10 02:57:16 UTC  
> Closed at: 2022-06-10 02:57:16 UTC  
> Url: https://github.com/boostorg/json/issues/717  

When parsing the int field information in JSON message on Solaris, the value obtained is incorrect. The order of the numbers is chaotic.  
Here are some codes.  
 "recvMsg " is a JSON string that needs to be parsed.  
```c++  
boost::json::object recvJson = boost::json::parse(recvMsg.c_str()).as_object();  
boost::json::array playSequence = recvJson["playSequence"].as_array();  
for (boost::json::value info : playSequence)  
{  
    boost::json::object infoJson = info.as_object();  
    long long cameraId = infoJson["cameraNumber"].as_int64();  
    printf("cameraid : %lld\n",cameraId);  
}  
  
```  
The correct value is 11739859, but the actual value is 37119589. The order of numbers has changed.  
We tested normally on windows10 (with MSVC v143), but there was a problem on Solaris 11.4.42.111.0.(sparcv9 ,with GCC v11.2.0).  
We tested 1_ 78_ 0 and 1_ 79_ 0, their performance is the same.  
![2022-06-08 104326](https://user-images.githubusercontent.com/73214592/172815809-79605997-c723-4c20-84cb-45b889cdb0d4.png)

---

## Comment 1

> Username: pdimov  
> Created at: 2022-06-09 13:17:16 UTC  
> Url: https://github.com/boostorg/json/issues/717#issuecomment-1151107758  

Endianness issue?

---

## Comment 2

> Username: pdimov  
> Created at: 2022-06-09 13:22:24 UTC  
> Url: https://github.com/boostorg/json/issues/717#issuecomment-1151113407  

11,739,859 = 0xB322D3  
37,119,589 = 0x2366665  
  
doesn't look like it.  
  
Oh, the digits are swapped. 1173 -> 3711, 9859 -> 9589. https://github.com/boostorg/json/blob/4797be3eac137729679403b89bd4f8580797737b/include/boost/json/detail/sse2.hpp#L337-L342  
  
BOOST_JSON_BIG_ENDIAN isn't being set correctly (that is, at all).  
  
https://github.com/boostorg/json/issues/131  
  
See here for the logic for detecting endianness: https://github.com/boostorg/endian/blob/develop/include/boost/endian/detail/order.hpp

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-06-09 13:44:14 UTC  
> Url: https://github.com/boostorg/json/issues/717#issuecomment-1151139499  

We'll just add a dependency on Endian

---

## Comment 4

> Username: pdimov  
> Created at: 2022-06-09 13:52:58 UTC  
> Url: https://github.com/boostorg/json/issues/717#issuecomment-1151150221  

Why? Just define the macro in config.hpp properly.  
```  
#if defined(__BYTE_ORDER__) && defined(__ORDER_BIG_ENDIAN__) && __BYTE_ORDER__ == __ORDER_BIG_ENDIAN__  
# define BOOST_JSON_BIG_ENDIAN  
#elif defined(__BIG_ENDIAN__)  
# define BOOST_JSON_BIG_ENDIAN  
#endif  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-06-09 13:56:36 UTC  
> Url: https://github.com/boostorg/json/issues/717#issuecomment-1151154309  

As a workaround for now the user can set the macro when building the lib

---

## Comment 6

> Username: 1NF1N1TTY  
> Created at: 2022-06-10 02:55:32 UTC  
> Url: https://github.com/boostorg/json/issues/717#issuecomment-1151870730  

After defining "BOOST_JSON_BIG_ENDIAN" in "config.hpp", the problem was solved. Thank you for your help.
