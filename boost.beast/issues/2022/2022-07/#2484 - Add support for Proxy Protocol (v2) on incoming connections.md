# #2484 - Add support for Proxy Protocol (v2) on incoming connections [Closed]

> Username: frittentheke  
> Created at: 2022-07-14 15:10:54 UTC  
> Updated at: 2024-04-03 07:03:22 UTC  
> Closed at: 2022-10-04 07:31:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2484  

### Version of Beast  
  
master  
  
### Steps necessary to reproduce the problem  
  
I'd like to ask you to look into supporting proxy protocol v2 which allows proxy servers / TCP load balancers to forward information  about a client to the backend.  
  
HAProxy does supports and actually originates this binary protocol specifying all sorts of fields to be piggybacked to the first TCP packet to the backend. This is instead of using HTTP headers the likes of `X-Forwarded-For`, `X-Forwarded-Proto`, which obviously only work when speaking HTTP and have other drawbacks.  
  
 * https://www.haproxy.com/blog/use-the-proxy-protocol-to-preserve-a-clients-ip-address/  
 * https://github.com/haproxy/haproxy/blob/master/doc/proxy-protocol.txt (**includes sample code**)  
  
But apart from HAProxy there are many more load balancers providing source info via proxy protocol v2 on outgoing connections to their backends. To not just name services from AWS, Azure, GCP which do support it for TCP connections via their load balancers or other networking services which terminate a TCP connection and would therefore "NAT" away source info.  
  
The proxy protocol v2 can also carry info like client certificates in case of TLS offloading and last but lot least is extensible via custom TLVs (type, length, value). Implementations of the proxy protocol v2 sometimes internally replace data like source ip and port with the values received via proxy protocol v2 and treat the connection as it was directly established this way. So logging, rate limits, ACLs and other uses of this data then transparently uses the actual client info and are not reduced to only seeing the load balancer as client.  
  
  
To give you a bit of back story ... I am a user of Ceph which uses Beast in their S3/Swift object storage service RGW (Rados Rateway),  
see:  
 * https://docs.ceph.com/en/quincy/radosgw/frontends/#beast  
 * https://github.com/ceph/ceph/blob/main/src/rgw/rgw_asio_frontend.cc  
  
RGW is usually deployed in multiple instances and placed behind a load balancer, like HAProxy. And while it's certainly possible to have HAProxy (https://cbonte.github.io/haproxy-dconv/2.0/configuration.html#option%20forwardfor) or and other HTTP reverse proxy to send headers which Ceph RGW using beast then logs (`rgw_log_http_headers`) this does not work for load balancers in TCP.  
  
  
  
### All relevant compiler information  
-

---

## Comment 1

> Username: frittentheke  
> Created at: 2022-07-14 15:18:35 UTC  
> Updated at: 2022-07-14 15:20:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1184574208  

Maybe I was too quick in opening this PR against Beast, as maybe there needs to be support for proxy protocol in https://github.com/boostorg/asio/blob/master/include/boost/asio/ip/tcp.hpp first before Beast can make use of it - @chriskohlhoff please kindly advise.

---

## Comment 2

> Username: sehe  
> Created at: 2022-07-16 00:07:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1186032184  

You're right that it might make more sense for this to be in Asio, it could be a separate library.  
  
Note that there is work in progress for SOCKS protocol here: https://github.com/alandefreitas/socks_proto   
  
It looks like it takes no dependency on Beast except for the examples.

---

## Comment 3

> Username: frittentheke  
> Created at: 2022-07-21 19:56:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1191876520  

@sehe @alandefreitas  I opened https://github.com/chriskohlhoff/asio/issues/1091 with asio in regards to support for the proxy protocol v2.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-07-22 15:48:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1192710206  

I don't see that being added to Asio but you never know.

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-07-22 15:53:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1192714260  

What is the official protocol spec?

---

## Comment 6

> Username: frittentheke  
> Created at: 2022-07-22 21:01:49 UTC  
> Updated at: 2022-07-22 21:02:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1192932128  

> What is the official protocol spec?  
  
@alandefreitas https://github.com/haproxy/haproxy/blob/master/doc/proxy-protocol.txt  
  
While you might say this is not exactly a standard in the form of an RFC or ISO norm - just look at how widely this is adopted   
(there is a list in https://www.haproxy.com/blog/use-the-proxy-protocol-to-preserve-a-clients-ip-address/).

---

## Comment 7

> Username: alandefreitas  
> Created at: 2022-07-22 22:31:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1192981159  

If I understand correctly, as an opaque proxy over TCP, this would be a project extremely similar to https://github.com/alandefreitas/socks_proto with other data structures. Even the notation for "client", "proxy server", and "application server" can remain almost the same.  
  
Like https://github.com/alandefreitas/socks_proto, this is just one more application of Asio. It belongs neither in Asio nor in Beast.

---

## Comment 8

> Username: frittentheke  
> Created at: 2022-07-23 19:31:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1193177281  

Maybe there are similarities. But please understand that the proxy protocol v2 is NOT an application layer protocol. It's just a little bit of structured data at the beginning of an incoming TCP (layer 4) connection. That's all there is, no response or further protocol commands or anything.  
  
A service receiving the connection, if it supports it, can then use to extract some data. There is not further "protocol" or any other applications for this. SOCKS on the other hand is a real service in itself relaying connections and following the client/server model.

---

## Comment 9

> Username: alandefreitas  
> Created at: 2022-07-23 21:36:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1193191847  

> Maybe there are similarities  
  
I don't see any relevant difference that justifies Proxy v2 not being its own library.  
  
> proxy protocol v2 is NOT an application layer protocol  
  
Yes. Neither is SOCKS.  
  
And Beast is an application layer protocol, so it's one more reason why Proxy v2 shouldn't be part of Beast.  
  
> just a little bit of structured data at the beginning of an incoming TCP  
  
So is SOCKS (unless you agree on the initial message to encrypt the data)  
  
> no response or further protocol commands or anything  
  
Just like SOCKS. SOCKS doesn't even wrap the messages in anything.  
  
It looks like you can get to Proxy v2 by just adapting socks_proto a little bit.

---

## Comment 10

> Username: frittentheke  
> Created at: 2022-07-25 08:20:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1193736046  

Thanks again for the fruitful discussion!  
  
> > Maybe there are similarities  
>   
> I don't see any relevant difference that justifies Proxy v2 not being its own library.  
  
I never really tried to argue there - I simply don't have the insight into how things are best structured.  
I just wanted to ask for the functionality of proxy protocol v2 data to be available for connections to Beast.  
  
Would there be a better place or way to discuss something "new" (as in a new library) that is neither part or core feature to asio or Beast? I raised the feature request with both, but understand you'd rather see this as an independent lib:  
  
 * https://github.com/chriskohlhoff/asio/issues/1091  
 * https://github.com/boostorg/beast/issues/2484 (this)  
   
 but understand that you would rather treat this like the SOCKS library.  
  
> > proxy protocol v2 is NOT an application layer protocol  
>   
> Yes. Neither is SOCKS.  
>   
> And Beast is an application layer protocol, so it's one more reason why Proxy v2 shouldn't be part of Beast.  
  
As said above (https://github.com/boostorg/beast/issues/2484#issuecomment-1184574208) I totally agree - I was just too quick in not seeing that Beast doe not do it's own TCP socket handling, but uses asio.  
  
The only time Beast would come in, is when the fields from the proxy protocol v2 are available from the asio handled socket to then make some use of that data later. So e.g. to log the source ip or use TLS client certificate (if that's even available as feature).  
  
> > just a little bit of structured data at the beginning of an incoming TCP  
>   
> So is SOCKS (unless you agree on the initial message to encrypt the data)  
>   
> > no response or further protocol commands or anything  
>   
> Just like SOCKS. SOCKS doesn't even wrap the messages in anything.  
>   
> It looks like you can get to Proxy v2 by just adapting socks_proto a little bit.  
  
  
If you don't mind me asking: Would Proxy v2 support then something you would implement based on the existing SOCKS code?

---

## Comment 11

> Username: alandefreitas  
> Created at: 2022-07-25 15:43:01 UTC  
> Updated at: 2022-07-25 15:44:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1194239509  

> Would there be a better place or way to discuss something "new" (as in a new library) that is neither part or core feature to asio or Beast?   
  
To be honest, I've never seen that in my life.  
  
> Would Proxy v2 support then something you would implement based on the existing SOCKS code?  
  
The SOCKS implementation isn't complete yet because we've been focused on some other projects over the last months (namely Boost.URL). The Proxy v2 implementation would not be very complex at all, but it takes a lot of time to read the specs, identify use cases, design the API, test everything, write the documentation, and come up with examples.  
  
I don't think Proxy v2 is something we would mind implementing as a separate lib. But considering the limited time and the number of issues we already have to fix, I don't think it's something on which we would focus unless there are many people clamoring for it.

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2022-08-30 09:37:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1231420155  

I think it should be it's own thing; I could help on the asio side if you, Christian, can get some work done on the protocol. I don't think I have the time to implement the full thing.  
  
You'd probably want it to be a stream around a tcp::socket like so:  
  
```cpp  
haproxy<tcp::socket> stream;  
stream.async_read_some(...);  
```  
  
i.e. modeled on ssl:;stream. But I don't know enough about the problem domain.

---

## Comment 13

> Username: madmongo1  
> Created at: 2022-08-30 09:41:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1231425176  

We already went through the investigation of adding SOCKS support to beast. SOCKS4 is trivial. SOCKS5 (the one everyone uses) is a massive headache that requires heavy involvement and knowledge of the underlying OS/environment.  
We decided it was well out of scope.

---

## Comment 14

> Username: klemens-morgenstern  
> Created at: 2022-09-24 04:26:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-1256852885  

@frittentheke since this is out of scope, can I close the issue? I think it won't be done as asio either, it'll probably need to be it's own library, e.g. `boost.haproxy`.

---

## Comment 15

> Username: ecorm  
> Created at: 2024-04-02 20:38:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-2033049229  

I don't use Proxy Protocol, but I've stumbled upon this issue via Internet search. It seems to me that we need to rethink C++ networking sockets so that intermediary layers can be added dynamically while maintaining the same abstract "asynchronous byte stream" interface without the application writer having to do `proto1stream<proto2stream<tcp::socket>>>` with templates.  
  
I think we've gone too far with templates in the C++ networking world, and need to be less averse to virtual functions. Is virtual function overhead really that significant in this domain? Especially when these virtual functions end up having to make OS calls?  
  
With protocol layers being insertable at runtime, it think it makes is less messy when a base protocol can be upgraded to a higher-level one dynamically, based on opening handshakes.  
  
I would like people smarter than me to rethink this layering stuff to make it simpler, so that I can focus more on the application-level stuff.

---

## Comment 16

> Username: frittentheke  
> Created at: 2024-04-03 07:03:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2484#issuecomment-2033710649  

Thanks @ecorm for your response. Maybe check out my other issue and comment https://github.com/chriskohlhoff/asio/issues/1091#issuecomment-1189064938. I believe PROXYv2 is just a simple extension on the TCP socket handling. Think of it as "TCP connection metadata" extending on just having the Source-IP+Port // Destination-IP+Port.  
  
Thus I opened this other issue with asio to ask for it to be added to the TCP layer.
