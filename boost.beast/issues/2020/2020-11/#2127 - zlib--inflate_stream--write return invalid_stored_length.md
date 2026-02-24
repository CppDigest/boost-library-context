# #2127 - zlib::inflate_stream::write return invalid_stored_length [Closed]

> Username: Appendme  
> Created at: 2020-11-26 15:26:53 UTC  
> Updated at: 2024-06-06 05:34:22 UTC  
> Closed at: 2022-01-09 05:17:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2127  

Is this a misuse of the library or a bug in the library itself?  
  
Code:  
https://gist.github.com/Appendme/ab71e9e608df23af37db168f7aa04083  
  
The error is returned here:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/zlib/detail/inflate_stream.ipp#L188  
  
Console output:  
Beast inflate:  
invalid stored block length  
  
Zlib inflate:  
{"t":null,"s":null,"op":10,"d":{"heartbeat_interval":41250,"_trace":["[\"gateway-prd-main-nw97\",{\"micros\":0.0}]"]}}  
  
### Version of Beast  
#define BOOST_BEAST_VERSION 300  
installed from vcpkg  
  
### All relevant compiler information  
Windows(x64):  
-- The CXX compiler identification is MSVC 19.28.29334.0  
Linux:  
-- The CXX compiler identification is GNU 9.3.0

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-11-26 16:18:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2127#issuecomment-734387581  

Thank you for providing such a wonderful test case. I've just got into the office. I'll take a look.

---

## Comment 2

> Username: Appendme  
> Created at: 2020-11-28 09:51:50 UTC  
> Updated at: 2020-11-29 08:39:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2127#issuecomment-735208479  

@madmongo1 added one more file https://gist.github.com/Appendme/ab71e9e608df23af37db168f7aa04083#file-inflate_v2-cpp  
  
Output:  
invalid stored block length  
zlib_inf == message_inf: true  
zlib_def == message_def: true  
beast_inf == message_inf: false  
beast_def == message_def: false  
message_def:  
789C34C9410A83301005D0BBFC755262A994CE558CC854873610A3C4B15242EE6E37DD3D78050A4A7B8C06DB1FCB0A6A9CC1042A780B677D0AEB10924AFE7004DD9A6BFBFB41338F02EAD079BC58E5E0AF5DF364670EC9A6E371F730C5630E635E360F7217577BF4B59E000000FFFF  
message_inf:  
{"t":null,"s":null,"op":10,"d":{"heartbeat_interval":41250,"_trace":["[\"gateway-prd-main-nw97\",{\"micros\":0.0}]"]}}  
zlib_def:  
789C34C9410A83301005D0BBFC755262A994CE558CC854873610A3C4B15242EE6E37DD3D78050A4A7B8C06DB1FCB0A6A9CC1042A780B677D0AEB10924AFE7004DD9A6BFBFB41338F02EAD079BC58E5E0AF5DF364670EC9A6E371F730C5630E635E360F7217577BF4B59E000000FFFF  
zlib_inf:  
{"t":null,"s":null,"op":10,"d":{"heartbeat_interval":41250,"_trace":["[\"gateway-prd-main-nw97\",{\"micros\":0.0}]"]}}  
beast_def:  
34C9410A83301005D0BBFC755262A994CE558CC854873610A3C4B15242EE6E37DD3D78050A4A7B8C06DB1FCB0A6A9CC1042A780B677D0AEB10924AFE7004DD9A6BFBFB41338F02EAD079BC58E5E0AF5DF364670EC9A6E371F730C5630E635E360F7217577BF4B59E000000FFFF  
beast_inf:  
  
upd. zlib_inflate(beast_def) return Z_DATA_ERROR(incorrect header check).

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 17:10:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2127#issuecomment-850866692  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2127#issuecomment-1008232327  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
