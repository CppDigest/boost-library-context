# #2282 - undefined behavior in boost::beast::detail::base64::decode [Closed]

> Username: saurik  
> Created at: 2021-07-12 02:15:52 UTC  
> Updated at: 2022-10-05 02:04:07 UTC  
> Closed at: 2022-10-05 02:04:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2282  

I don't like the entire premise of compilers taking advantage of undefined behavior to elide, but I certainly fear it :(. There is a check in clang-tidy called clang-analyzer-core.UndefinedBinaryOperatorResult that is flagging boost::beast::detail::base64::decode (note that I'm on Boost 1.76.0, which seems to be Beast 313).  
  
It could be this check is flawed--I've certainly seen a lot of bugs (not just false positives in flakey checks, but incorrectly coded checks) in clang-tidy--but this one sort of makes sense to me: if you give it a very short string, then c4 will have only been partially initialized, which will only partially initialize c3.  
  
I would hope that the compiler wouldn't be frustrating enough to do anything with this knowledge other than cut the loop short--which is "fine", as the loop only uses the correct amount of c3--but I figure that compiler people love to do strange things like not bother to terminate functions, etc. :(.  
  
```  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:184:48: error: The left operand of '&' is a garbage value [clang-analyzer-core.UndefinedBinaryOperatorResult,-warnings-as-errors]  
        c3[0] = ( c4[0]        << 2) + ((c4[1] & 0x30) >> 4);  
                                               ^  
/home/saurik/orchid/cli-shared/vpn/p2p/source/base64.cpp:41:23: note: Calling 'decode'  
    const auto result(decode(decoded.data(), data.data(), data.size()));  
                      ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Assuming the condition is true  
    while(len-- && *in != '=')  
          ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Left side of '&&' is true  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:20: note: Assuming the condition is true  
    while(len-- && *in != '=')  
                   ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:5: note: Loop condition is true.  Entering loop body  
    while(len-- && *in != '=')  
    ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:166:12: note: Assuming the condition is false  
        if(v == -1)  
           ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:166:9: note: Taking false branch  
        if(v == -1)  
        ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:170:9: note: Taking false branch  
        if(++i == 4)  
        ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Assuming the condition is false  
    while(len-- && *in != '=')  
          ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:17: note: Left side of '&&' is false  
    while(len-- && *in != '=')  
                ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:182:8: note: 'i' is 1  
    if(i)  
       ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:182:5: note: Taking true branch  
    if(i)  
    ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:184:48: note: The left operand of '&' is a garbage value  
        c3[0] = ( c4[0]        << 2) + ((c4[1] & 0x30) >> 4);  
                                               ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:185:48: error: The left operand of '&' is a garbage value [clang-analyzer-core.UndefinedBinaryOperatorResult,-warnings-as-errors]  
        c3[1] = ((c4[1] & 0xf) << 4) + ((c4[2] & 0x3c) >> 2);  
                                               ^  
/home/saurik/orchid/cli-shared/vpn/p2p/source/base64.cpp:41:23: note: Calling 'decode'  
    const auto result(decode(decoded.data(), data.data(), data.size()));  
                      ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Assuming the condition is true  
    while(len-- && *in != '=')  
          ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Left side of '&&' is true  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:20: note: Assuming the condition is true  
    while(len-- && *in != '=')  
                   ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:5: note: Loop condition is true.  Entering loop body  
    while(len-- && *in != '=')  
    ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:166:12: note: Assuming the condition is false  
        if(v == -1)  
           ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:166:9: note: Taking false branch  
        if(v == -1)  
        ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:170:9: note: Taking false branch  
        if(++i == 4)  
        ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Assuming the condition is true  
    while(len-- && *in != '=')  
          ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Left side of '&&' is true  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:20: note: Assuming the condition is true  
    while(len-- && *in != '=')  
                   ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:5: note: Loop condition is true.  Entering loop body  
    while(len-- && *in != '=')  
    ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:166:12: note: Assuming the condition is false  
        if(v == -1)  
           ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:166:9: note: Taking false branch  
        if(v == -1)  
        ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:170:9: note: Taking false branch  
        if(++i == 4)  
        ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Assuming the condition is false  
    while(len-- && *in != '=')  
          ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:17: note: Left side of '&&' is false  
    while(len-- && *in != '=')  
                ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:182:8: note: 'i' is 2  
    if(i)  
       ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:182:5: note: Taking true branch  
    if(i)  
    ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:185:48: note: The left operand of '&' is a garbage value  
        c3[1] = ((c4[1] & 0xf) << 4) + ((c4[2] & 0x3c) >> 2);  
                                               ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:186:38: error: The right operand of '+' is a garbage value [clang-analyzer-core.UndefinedBinaryOperatorResult,-warnings-as-errors]  
        c3[2] = ((c4[2] & 0x3) << 6) +   c4[3];  
                                     ^  
/home/saurik/orchid/cli-shared/vpn/p2p/source/base64.cpp:41:23: note: Calling 'decode'  
    const auto result(decode(decoded.data(), data.data(), data.size()));  
                      ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Assuming the condition is true  
    while(len-- && *in != '=')  
          ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Left side of '&&' is true  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:20: note: Assuming the condition is true  
    while(len-- && *in != '=')  
                   ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:5: note: Loop condition is true.  Entering loop body  
    while(len-- && *in != '=')  
    ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:166:12: note: Assuming the condition is false  
        if(v == -1)  
           ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:166:9: note: Taking false branch  
        if(v == -1)  
        ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:170:9: note: Taking false branch  
        if(++i == 4)  
        ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Assuming the condition is true  
    while(len-- && *in != '=')  
          ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Left side of '&&' is true  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:20: note: Assuming the condition is true  
    while(len-- && *in != '=')  
                   ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:5: note: Loop condition is true.  Entering loop body  
    while(len-- && *in != '=')  
    ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:166:12: note: Assuming the condition is false  
        if(v == -1)  
           ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:166:9: note: Taking false branch  
        if(v == -1)  
        ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:170:9: note: Taking false branch  
        if(++i == 4)  
        ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Assuming the condition is true  
    while(len-- && *in != '=')  
          ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Left side of '&&' is true  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:20: note: Assuming the condition is true  
    while(len-- && *in != '=')  
                   ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:5: note: Loop condition is true.  Entering loop body  
    while(len-- && *in != '=')  
    ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:166:12: note: Assuming the condition is false  
        if(v == -1)  
           ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:166:9: note: Taking false branch  
        if(v == -1)  
        ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:170:9: note: Taking false branch  
        if(++i == 4)  
        ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:11: note: Assuming the condition is false  
    while(len-- && *in != '=')  
          ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:163:17: note: Left side of '&&' is false  
    while(len-- && *in != '=')  
                ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:182:8: note: 'i' is 3  
    if(i)  
       ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:182:5: note: Taking true branch  
    if(i)  
    ^  
./vpn/p2p/boost/libs/beast/include/boost/beast/core/detail/base64.ipp:186:38: note: The right operand of '+' is a garbage value  
        c3[2] = ((c4[2] & 0x3) << 6) +   c4[3];  
                                     ^  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-07-12 02:17:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2282#issuecomment-877920564  

Can you please write a very small test function which demonstrates the problem? We do run this through sanitizers with every commit, and this would ensure that the problem becomes visible.

---

## Comment 2

> Username: saurik  
> Created at: 2021-07-12 02:24:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2282#issuecomment-877922497  

```c++  
#include <iostream>  
#include <boost/beast/core/detail/base64.hpp>  
  
int main(int argc, const char *argv[]) {  
    using namespace boost::beast::detail::base64;  
    std::string encoded(argv[1]);  
    std::string decoded;  
    decoded.resize(decoded_size(encoded.size()));  
    const auto result(decode(&decoded[0], encoded.data(), encoded.size()));  
    decoded.resize(result.first);  
    std::cout << decoded << std::endl;  
    return 0;  
}  
```  
  
```  
$ clang++ -o de64 de64.cpp && ./de64 $(echo -n hello | openssl enc -base64)  
hello  
```  
  
```  
$ clang-tidy de64.cpp  
Error while trying to load a compilation database:  
Could not auto-detect compilation database for file "de64.cpp"  
No compilation database found in /home/saurik/orchid/cli-shared or any parent directory  
fixed-compilation-database: Error while opening fixed database: No such file or directory  
json-compilation-database: Error while opening JSON database: No such file or directory  
Running without flags.  
3 warnings generated.  
/usr/include/boost/beast/core/detail/base64.ipp:184:48: warning: The left operand of '&' is a garbage value [clang-analyzer-core.UndefinedBinaryOperatorResult]  
        c3[0] = ( c4[0]        << 2) + ((c4[1] & 0x30) >> 4);  
                                               ^  
... [ all that output from earlier ] ...  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-07-12 02:34:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2282#issuecomment-877925242  

I guess we should simply memset the `c` array to zero, would that fix it?

---

## Comment 4

> Username: saurik  
> Created at: 2021-07-12 02:44:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2282#issuecomment-877927939  

FWIW, the following happens to be sufficient to satisfy clang-analyzer-core.UndefinedBinaryOperatorResult.  
  
```diff  
diff --git a/include/boost/beast/core/detail/base64.ipp b/include/boost/beast/core/detail/base64.ipp  
index 4fc3c677..55f9be16 100644  
--- a/include/boost/beast/core/detail/base64.ipp  
+++ b/include/boost/beast/core/detail/base64.ipp  
@@ -154,7 +154,7 @@ decode(void* dest, char const* src, std::size_t len)  
 {  
     char* out = static_cast<char*>(dest);  
     auto in = reinterpret_cast<unsigned char const*>(src);  
-    unsigned char c3[3], c4[4];  
+    unsigned char c3[3], c4[4] = {0};  
     int i = 0;  
     int j = 0;  
  
```

---

## Comment 5

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2282#issuecomment-1008220765  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: saurik  
> Created at: 2022-04-14 07:43:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2282#issuecomment-1098806676  

No: it has not been resolved.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-04-14 19:03:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2282#issuecomment-1099535947  

ack
