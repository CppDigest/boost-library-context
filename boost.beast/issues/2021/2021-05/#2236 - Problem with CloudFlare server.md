# #2236 - Problem with CloudFlare server [Closed]

> Username: treyweaver  
> Created at: 2021-05-21 22:14:40 UTC  
> Updated at: 2021-05-25 11:36:56 UTC  
> Closed at: 2021-05-25 11:36:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2236  

Version that comes with boost 1.74  
  
I am unable to get the sample websocket_client_async_ssl.cpp to connect  
to wss://zellowork.io on a cloudflare server.  
  
https://github.com/boostorg/beast/blob/develop/example/websocket/client/  
async-ssl/websocket_client_async_ssl.cpp  
  
The error I get during on "on_ssl_handshake" is "sslv3 alert handshake  
failure"  
  
No special certificates or keys are needed because I can connect  
straight-away with wscat or websoccat or even curl if I use curl:   
        curl -vv https://zellowork.io/ws/mesh

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-22 04:21:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-846347117  

Hi there. What did you type on the command line?

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-05-22 05:44:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-846354797  

I seem to remember having to update the certificates that are hard-code in the source. I think that was in boost 1.75  
  
I'll run the latest version here and see if that helps.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-05-22 05:59:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-846356449  

OK, the problem is that the examples only include a small set of the root certificates that are in use today and your server is presenting a certificate not recognised by the example.  
  
I fixed this by copying the contents of the file `/etc/ssl/certs/ca-bundle.crt` into the function body of `load_root_certificates()`.  
  
Here is the commit:  
  
https://github.com/boostorg/beast/pull/2237/commits/ee36fced197b4cb8c3b400c12877c7bf207583ef

---

## Comment 4

> Username: treyweaver  
> Created at: 2021-05-22 17:32:04 UTC  
> Updated at: 2021-05-22 17:33:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-846440060  

I tried the new load_root_certificates()  and got the same result.  
  
The root_certificates.hpp file I tried was 265114 bytes long; just to make sure I am using the correct one.  
  
Or where you saying that I need to use my own /etc/ssl/certs/ca-bundle.crt file?  I don't have that file but I do have a ca-certificates.crt file.  (Debian 10)  
  
Thanks for your help.

---

## Comment 5

> Username: treyweaver  
> Created at: 2021-05-22 21:21:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-846465056  

Richard,  
  
Can you reopen this?  This fix did not fix my issue.  I used openssl to get the certificates from zelloworks.io and that did not seem to work either.  
  
Thanks for your help!  
Trey Weaver  
  
  
> On May 22, 2021, at 2:00 AM, Richard Hodges ***@***.***> wrote:  
>   
>   
> OK, the problem is that the examples only include a small set of the root certificates that are in use today and your server is presenting a certificate not recognised by the example.  
>   
> I fixed this by copying the contents of the file /etc/ssl/certs/ca-bundle.crt into the function body of load_root_certificates().  
>   
> Here is the commit:  
>   
> ee36fce <https://github.com/boostorg/beast/commit/ee36fced197b4cb8c3b400c12877c7bf207583ef>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/beast/issues/2236#issuecomment-846356449>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/ABJBASM54DQIQDFCGKYRXRTTO5B6BANCNFSM45J6CSTA>.  
>

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-05-23 14:20:55 UTC  
> Updated at: 2021-05-23 14:21:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-846570925  

In what way did it not work for you? When I ran the code it connected , performed the ssl handshake correctly and then waited.  
It then disconnected.  
This seems to be “correct” behaviour given the problem description.  
  
are you saying that for you the SSL layer does not handshake still?

---

## Comment 7

> Username: treyweaver  
> Created at: 2021-05-23 18:23:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-846604549  

Richard,  
  
The error that I am getting is:    
ssl_handshake: sslv3 alert handshake failure  
  
Same as before.  I am on a Debian-10 machine running on ARM.  Tomorrow morning I am planning on running the same code  Debian-10 machine on Intel to see if it has the same issue.  
  
Thanks again for your help!  
  
  
> On May 23, 2021, at 10:21 AM, Richard Hodges ***@***.***> wrote:  
>   
>   
> Reopened #2236 <https://github.com/boostorg/beast/issues/2236>.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/beast/issues/2236#event-4783384715>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/ABJBASP5UP7GKCF66TAXRH3TPEFNLANCNFSM45J6CSTA>.  
>

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-05-23 21:17:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-846625690  

Hmm. That doesn't make sense. When I applied the patch in the PR (now on develop branch) it fixes that problem for me.

---

## Comment 9

> Username: treyweaver  
> Created at: 2021-05-23 21:39:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-846628195  

The only file that you changed was the root_certificates.hpp file right?  Cause that was the only file I imported.  
  
I am able to run these command successfully:  
openssl s_client -connect zellowork.io:443 <http://zellowork.io:443/>  
openssl s_client -connect zellowork.io:443 <http://zellowork.io:443/> -servername zellowork.io <http://zellowork.io/>  
  
So I don’t think it is related to openssl.  
  
  
  
> On May 23, 2021, at 5:17 PM, Richard Hodges ***@***.***> wrote:  
>   
>   
> Hmm. That doesn't make sense. When I applied the patch in the PR (now on develop branch) it fixes that problem for me.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/beast/issues/2236#issuecomment-846625690>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/ABJBASKBETYXHG3MKGSKOO3TPFWG5ANCNFSM45J6CSTA>.  
>

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-05-23 23:44:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-846644909  

> ssl_handshake: sslv3 alert handshake failure  
  
This is unequivocally an SSL handshake error.  
  
Dumb question:  
Did you recompile after patching the root_certificates.hpp file?

---

## Comment 11

> Username: treyweaver  
> Created at: 2021-05-24 13:51:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-847056382  

I think I got it!  
  
If you set up the SNI host name as *<zellowork.io:443>* rather than just *<zellowork.io>* it fails.I don't know what combination of things causes this issue:  version of openssl, verison of ASIO, Cloudflare, Arm Processor or something else but it does fail.  
  
On open ssl on my machine this works:  
*openssl s_client -connect zellowork.io:443 -servername zellowork.io*  
but this fails  
*openssl s_client -connect zellowork.io:443 -servername zellowork.io:443*  
but these all work:  
*openssl s_client -connect mozilla.com:443 <http://zellowork.io:443/> -servername mozilla.com:443*  
*openssl s_client -connect google.com:443 <http://zellowork.io:443/> -servername google.com:443*  
*openssl s_client -connect hover.com:443 <http://zellowork.io:443/> -servername hover.com <http://zellowork.io/>:443*  
*openssl s_client -connect cloudflare.com:443 <http://zellowork.io:443/> -servername cloudflare.com:443*  
  
Thanks for all of your help!  
  
Here is my modified code for on_connect:  
  
*   ** void**  
*  
*    on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type ep)**  
*  
*    {**  
*  
*        std::cout << "********** on_connect..." << std::endl;**  
*  
*        if(ec)**  
*  
*            return fail(ec, "connect");**  
*  
*  
*  
*        // Update the host_ string. This will provide the value of the**  
*  
*        // Host HTTP header during the WebSocket handshake.**  
*  
*        // See **https://tools.ietf.org/html/rfc7230#section-5.4**  
*  
*        std::string jHost = host_;**  
*  
*        host_ += ':' + std::to_string(ep.port());**  
*  
*        std::cout << "Host: " <<  host_.c_str() << std::endl;**  
*  
*  
*  
*        // Set a timeout on the operation**  
*  
*        beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));**  
*  
*  
*  
*        // Set SNI Hostname (many hosts need this to handshake successfully)**  
*  
*        if(! SSL_set_tlsext_host_name(**  
*  
*                ws_.next_layer().native_handle(),**  
*  
*                jHost.c_str()))**  
*  
*        {**  
*  
*            ec = beast::error_code(static_cast<int>(::ERR_get_error()),**  
*  
*                net::error::get_ssl_category());**  
*  
*            return fail(ec, "connect");**  
*  
*        }**  
*  
*  
*  
*        // Perform the SSL handshake**  
*  
*        ws_.next_layer().async_handshake(**  
*  
*            ssl::stream_base::client,**  
*  
*            beast::bind_front_handler(**  
*  
*                &session::on_ssl_handshake,**  
*  
*                shared_from_this()));**  
*  
*    }**  
*  
**  
****  
  
On Sun, May 23, 2021, at 7:45 PM, Richard Hodges wrote:  
>   
  
>> ssl_handshake: sslv3 alert handshake failure  
  
> This is unequivocally an SSL handshake error.  
  
  
> Dumb question:  
> Did you recompile after patching the root_certificates.hpp file?  
  
  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/beast/issues/2236#issuecomment-846644909>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/ABJBASIRPXO5ED55RR6UBFLTPGHQNANCNFSM45J6CSTA>.

---

## Comment 12

> Username: madmongo1  
> Created at: 2021-05-24 14:05:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-847065754  

The command line arguments you should be giving to the beast program are:  
  
 `zellowork.io 443 /ws/mesh`

---

## Comment 13

> Username: treyweaver  
> Created at: 2021-05-24 14:09:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-847067992  

I actually hard coded the /ws/mesh into the code. So it is there.  
  
Trey  
  
On Mon, May 24, 2021, at 10:06 AM, Richard Hodges wrote:  
>   
  
> The command line arguments you should be giving to the beast program are:  
  
> `zellowork.io 443 /ws/mesh`  
  
  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/beast/issues/2236#issuecomment-847065754>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/ABJBASMOAJNIHCVR4Q56KFDTPJMNPANCNFSM45J6CSTA>.

---

## Comment 14

> Username: madmongo1  
> Created at: 2021-05-24 14:15:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-847072921  

OK Great, may I close this issue now?

---

## Comment 15

> Username: treyweaver  
> Created at: 2021-05-24 19:53:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2236#issuecomment-847294278  

Yes,  
  
Again thanks for your help!  
  
Trey  
  
On Mon, May 24, 2021, at 10:16 AM, Richard Hodges wrote:  
>   
  
> OK Great, may I close this issue now?  
  
  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/beast/issues/2236#issuecomment-847072921>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/ABJBASJ3IHHRZJY4MRDC2P3TPJNSFANCNFSM45J6CSTA>.
