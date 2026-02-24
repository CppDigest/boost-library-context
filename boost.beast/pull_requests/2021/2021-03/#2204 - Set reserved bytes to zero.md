# #2204 Set reserved bytes to zero [Closed]

> Username: sweemer  
> Created at: 2021-03-19 12:30:31 UTC  
> Updated at: 2021-03-21 00:03:51 UTC  
> Closed at: 2021-03-21 00:03:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2204  

Hello, first of all I would like to thank you very much for developing and maintaining beast - it has been very useful for me for a couple years now.  
  
Recently I've been trying to add compression to a websocket server implemented with beast through the `websocket::permessage_deflate` option. The problem is that beast assigns the first reserved bit to 1, which is not compatible with most client libraries, such as [System.Net.WebSockets](https://github.com/dotnet/runtime/blob/main/src/libraries/System.Net.WebSockets/src/Resources/Strings.resx#L118) in .NET, which throws `The WebSocket received a frame with one or more reserved bits set` upon receiving the first compressed message.  
  
This exception appears to agree with [section 5.2 of RFC6455](https://tools.ietf.org/html/rfc6455#section-5.2):  
  
>    RSV1, RSV2, RSV3:  1 bit each  
>   
>       MUST be 0 unless an extension is negotiated that defines meanings  
>       for non-zero values.  If a nonzero value is received and none of  
>       the negotiated extensions defines the meaning of such a nonzero  
>       value, the receiving endpoint MUST _Fail the WebSocket  
>       Connection_.  
  
If I simply set `rsv1` to zero then the C# client can process the incoming deflated messages without any issue. I believe that this should be the default behavior in boost, which is why I am submitting this pull request.  
  
In order for me to better understand why the code was implemented in this way, and to prime the discussion on this pull request, I would like to ask you the following questions.  
  
1. What was the rationale for setting the value of `rsv1` to 0 in case of uncompressed and 1 in case of a compressed stream?  
  
2. RFC6455 says that `rsv1` "MUST be 0 unless an extension is negotiated that defines meanings for non-zero values". Does beast implicitly negotiate such an extension; and if so, what is the nature of this extension?  
  
3. Is there a better way to adhere to the default requirement of setting `rsv1` to 0 to be compatible with client implementations; say perhaps by introducing a new option in [boost/beast/websocket/option.hpp](https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/option.hpp)?

---

## Comment 1

> Username: madmongo1  
> Created_at: 2021-03-19 13:07:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2204#issuecomment-802820297  

Thanks very much for the commit. This looks important.  
It’s causing all my unit tests for fail which is a concern!

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-03-19 13:25:55 UTC  
> Updated_at: 2021-03-19 13:36:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2204#issuecomment-802831084  

Is it likely that there has been a lingering bug for 5 years that prevents Beast websockets from being interoperable with major client implementations, which no one has noticed until now? Beast passes the Autobahn|Testsuite's permessage-deflate gauntlet of tests:  
https://vinniefalco.github.io/BeastAssets/reports/autobahn/index.html  
  
The RFC that you are looking for is RFC 7692 which covers the permessage-deflate extensions. From https://tools.ietf.org/html/rfc7692#section-1  
  
> PMCEs use the RSV1 bit of the WebSocket frame header to indicate whether a message is compressed or not so that an endpoint can choose not to compress messages with incompressible contents.  
  
Setting RSV1 is defined by the permessage-deflate spec. Does System.Net.WebSockets pass the Autobahn|Testsuite for clients?  
  
It looks like System.Net.WebSockets is the non-compliant implementation:  
https://www.tpeczek.com/2017/07/websocket-per-message-compression-in.html  
  
From aspnet/WebSockets#19 (comment)  
  
> The System.Net.WebSockets.WebSocket.SendAsync API is poorly suited to compression/extension.  
  
Sorry!!

---

## Comment 3

> Username: sweemer  
> Created_at: 2021-03-20 06:27:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2204#issuecomment-803259850  

@madmongo1 and @vinniefalco thanks for your quick responses and for the very helpful explanations. I understand now that RSV1 should indeed be set to 1 when the permessage-deflate extension is negotiated as per RFC 7692, so beast is doing the right thing.  
  
Based on the links you sent, .NET 6 (which is still in preview as of now) may fully support RFC 7692, which would be great. I'll look forward to trying it out soon.  
  
In the meantime, what do you think of my question 3 to introduce a new `websocket::permessage_deflate` option to force the server to set RSV1 to 0 even for compressed streams set to false by default? I understand that this is not compliant with RFC 7692, but it would certainly be useful as a way to facilitate migration of non-compliant client applications. That is, the websocket server can continue to leverage beast's implementation of permessage-deflate with this option set, and clients can perform their own decompression of the websocket message in the application layer. Then when the client library finally provides fully compliant support for RFC 7692 (such as in .NET 6), the option can be switched back to false on the server side.  
  
By the way, it looks like the websocket client in ASP.NET was [at one time tested](https://github.com/aspnet/WebSockets/blob/master/test/AutobahnTestApp/scripts/autobahn.spec.json) with Autobahn, but test cases 12.* and 13.* dealing with permessage-deflate were excluded, so it's pretty clear to me now that compressed was never actually supported.  
  
Also it looks like test cases 3.* dealing with reserved bits all require RSV1 to be 0, and there don't appear to be any cases that positively check for RSV1 set to 1 when permessage-deflate is enabled. This is perhaps something that should be added to Autobahn|Testsuite in order to expose client implementations that always require the reserved bits to be zero even for permessage-deflate.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2021-03-20 16:43:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2204#issuecomment-803401984  

> ...it would certainly be useful as a way to facilitate migration of non-compliant client applications.  
  
Beast is doing the right thing. The way to "facilitate migration" is to adhere to the spec, giving the non-compliant applications an incentive to follow the protocol. Otherwise, if we do as you suggest and set RSV1 to 0, the non-compliant application will continue to function and there will be no reason for the application to be fixed.

---

## Comment 5

> Username: sweemer  
> Created_at: 2021-03-21 00:03:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2204#issuecomment-803488033  

Ok fair enough. Thanks anyway for taking a look at this for me!

---
