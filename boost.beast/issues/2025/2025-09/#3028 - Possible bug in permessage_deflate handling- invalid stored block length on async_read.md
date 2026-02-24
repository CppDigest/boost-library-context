# #3028 - Possible bug in permessage_deflate handling: invalid stored block length on async_read [Closed]

> Username: shinny-zhangyihang  
> Created at: 2025-09-01 07:16:46 UTC  
> Updated at: 2025-09-04 04:45:24 UTC  
> Closed at: 2025-09-04 04:45:24 UTC  
> Url: https://github.com/boostorg/beast/issues/3028  

First of all, thank you for maintaining Boost.Beast! It’s an excellent library with great design and documentation, and it has been very helpful for our WebSocket projects.  
  
While using Boost.Beast extensively, we encountered an issue that seems unusual and might be a bug, specifically related to permessage-deflate compression in the WebSocket client.  
  
That said, when using Boost.Beast as a WebSocket client with permessage-deflate enabled, we occasionally encounter errors during `async_read`. Specifically, the error code returned is:  
`ec: invalid stored block length`  
  
We used the following WebSocket client configuration:  
  
```  
websocket::permessage_deflate pmd;  
pmd.client_enable = true;  
pmd.server_enable = true;   
pmd.client_no_context_takeover = false;   
pmd.server_no_context_takeover = false;  
ws.set_option(pmd);  
```  
  
During communication, `async_read `sometimes fails with the above error.  
  
We captured the traffic using Wireshark, and the problematic packet can be read and decompressed correctly by Wireshark, which suggests the issue may be in the client-side handling of compressed frames rather than in the network data.  
  
After further investigation, we were able to narrow down the potential cause. We created a minimal example that reliably reproduces the issue. In addition, we submitted a pull request proposing a fix, and testing shows that the proposed changes successfully resolve the problem:  
  
https://github.com/shinnytech/boost-beast/pull/1  
  
We realize that our understanding of the internal workings of Boost.Beast, especially in the permessage-deflate implementation, is limited. We would greatly appreciate it if the maintainers could take a closer look and provide guidance or suggestions for a more robust solution. Your expertise would be invaluable in helping us understand and properly address this issue.

---

## Comment 1

> Username: ashtum  
> Created at: 2025-09-01 08:30:48 UTC  
> Updated at: 2025-09-01 09:24:01 UTC  
> Url: https://github.com/boostorg/beast/issues/3028#issuecomment-3241396052  

Thank you for reporting the issue and for your thorough analysis.  
  
> When finishing a permessage-deflate message, Beast always appended the 4‑byte RFC7692 tail (00 00 FF FF) but did not track how many of those bytes zlib had consumed. With very small avail_out (e.g. 1 byte), zlib could consume only part of the tail and the next iteration would re-append from the beginning, corrupting the stored block length and producing "invalid stored block length".  
  
The current implementation relies on the assumption that zlib accepts empty block codes as long as there is at least 1 byte of space in the output buffer. Could you please provide a reproducible example that invalidates this assumption?  
You don't need to use the WebSocket interface for that, it can be done using the zlib interface.  
  
EDIT:  
This seems easily reproducible with a server/client instance and happens quite frequently. I'm investigating it further.  
No need for a reproducible example.

---

## Comment 2

> Username: ashtum  
> Created at: 2025-09-01 12:12:18 UTC  
> Updated at: 2025-09-01 12:14:34 UTC  
> Url: https://github.com/boostorg/beast/issues/3028#issuecomment-3242130718  

@shinny-zhangyihang, It seems the error is unrelated to partial consumption of appended empty block codes . Indeed zlib consistently accepts all 4 bytes regardless of output buffer size.  
https://github.com/boostorg/beast/blob/9b24b28d502946583300d3e58767cb30a8ec8486/include/boost/beast/websocket/impl/read.hpp#L584-L588  
  
You might confirm that by adding an assertion after `inflate`:  
```C++  
impl.inflate(zs, zlib::Flush::sync, ec);  
if(fin && zs.avail_in != 0)  
    throw std::logic_error("short write");  
```  
I think the root cause is appending more than one empty block code due to the small output buffer space. Your fix works because it also prevents that as a side effect.

---

## Comment 3

> Username: shinny-zhangyihang  
> Created at: 2025-09-03 02:51:31 UTC  
> Url: https://github.com/boostorg/beast/issues/3028#issuecomment-3247502310  

Thank you very much for the quick response and for submitting a PR to address this issue. We really appreciate the effort and the high quality of work you put into maintaining Boost.Beast.  
  
I tested the PR on my side, but unfortunately the problem still occurs. In some cases, async_read still fails with  
`ec: invalid stored block length`  
  
This suggests that there may still be some edge cases where the tail bytes are not handled correctly under certain buffer conditions.  
  
Could you please take another look? Your expertise would be greatly appreciated.  
  
  
You should be able to reproduce the error quite easily by running the example included in my PR and following the steps in the README. https://github.com/shinnytech/boost-beast/pull/1  
  
Alternatively, you can also download the attached files, which contain minimal code to reproduce the issue:  
```  
client_smallbuf.cpp  
server_pmd_writer.cpp  
```  
  
[reproduce.zip](https://github.com/user-attachments/files/22107489/reproduce.zip)

---

## Comment 4

> Username: ashtum  
> Created at: 2025-09-03 10:27:50 UTC  
> Updated at: 2025-09-03 10:28:26 UTC  
> Url: https://github.com/boostorg/beast/issues/3028#issuecomment-3248659247  

You are right, there was another corner case where the internal buffer of the zlib stream gets full and can't fully consume the empty block codes. I have updated the tests accordingly, and the code now handles this case (PR is updated).  
  
I also tested your reproducible example, and it seems to be fixed now.  
Thank you very much for testing the code on your end, fixing this issue would not have been possible otherwise.

---

## Comment 5

> Username: shinny-zhangyihang  
> Created at: 2025-09-04 01:38:37 UTC  
> Url: https://github.com/boostorg/beast/issues/3028#issuecomment-3251382947  

Thank you for the fix and the quick turnaround!   
I just tested the latest PR locally, and the issue is now completely resolved. Everything works perfectly on my side.  
  
Really appreciate your expertise and the high quality of your work
