# #2340 - permessage-deflate client offer rejected [Closed]

> Username: rpfisker  
> Created at: 2021-11-08 18:26:56 UTC  
> Updated at: 2022-09-24 05:08:53 UTC  
> Closed at: 2022-09-24 05:08:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2340  

### Version of Beast  
  
318  
  
### Steps necessary to reproduce the problem  
  
(Unfortunately we cannot share the entire program)  
  
Recently in our project, we have tried to use the permessage-deflate feature to enable compression from a websocket client to the server. Our server side is implemented in Go and uses the gobwas websocket library. I added these lines in the websocket handshake code to enable compression negotiation in our C++ client:  
  
// C++ client  
  
// type aliases  
namespace net = boost::asio;  
using tcp = net::ip::tcp;  
using executor_type = net::strand<net::io_context::executor_type>;  
using stream_type = beast::basic_stream<tcp, executor_type>;  
using wss_stream_type=websock::stream<beast::ssl_stream<stream_type>, true>  
  
wss_stream_type ws_;  
...  
boost::beast::websocket::permessage_deflate opt;  
opt.client_enable = true; // for clients  
opt.server_enable = true; // for servers  
ws_->set_option(opt);  
   
The generated HTTP header:  
   
Sec-WebSocket-Extensions: permessage-deflate; server_max_window_bits=15; client_max_window_bits=15  
   
The server in Go rejected our offer and we couldn’t establish compression.  
  
  
Meanwhile, we had another client written in python. Connecting to the same websocket server written in Go, we connect the python client using the following connection setup:  
  
\# python client  
  
async with websockets.connect(self.url, compression='deflate', extra_headers=self.header(), ssl=ssl_context) as self.websocket:  
   
The generated HTTP header in this case:  
   
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits  
   
The Go server accepted the offer and we established compression.  
  
  
Local fix:  
  
Since the python code succeeded but the C++ code did not, we focused on the difference between the headers. We made a change to the beast code to get it to produce the same header as python, and then our negotiation succeeded. We were able to verify that compression outbound from C++ was working. We’re not sure if decompression works at this point.  
  
Here is the change that we made:  
  
In file \<beast root\>/websocket/detail/impl_base.hpp:  
   
    void  
    set_option_pmd(permessage_deflate const& o)  
    {  
        if( (o.server_max_window_bits > 15 ||  
            o.server_max_window_bits < 9)  
            && (o.server_max_window_bits != 0 &&  // added  
            o.server_max_window_bits != -1))            // added  
            BOOST_THROW_EXCEPTION(std::invalid_argument{  
                "invalid server_max_window_bits"});  
        if( (o.client_max_window_bits > 15 ||  
            o.client_max_window_bits < 9)  
            && (o.client_max_window_bits != 0 &&    // added  
            o.client_max_window_bits != -1)).             // added  
            BOOST_THROW_EXCEPTION(std::invalid_argument{  
                "invalid client_max_window_bits"});  
        if( o.compLevel < 0 ||  
            o.compLevel > 9)  
            BOOST_THROW_EXCEPTION(std::invalid_argument{  
                "invalid compLevel"});  
        if( o.memLevel < 1 ||  
            o.memLevel > 9)  
            BOOST_THROW_EXCEPTION(std::invalid_argument{  
                "invalid memLevel"});  
        pmd_opts_ = o;  
    }  
   
   
After making this change, we changed the C++ client code to specify the window size:  
  
websock::permessage_deflate opt;  
opt.client_enable = true; // for clients  
opt.client_max_window_bits = -1;      // added  
opt.server_enable = true; // for servers  
opt.server_max_window_bits = 0;      // added  
ws_->set_option(opt);  
   
   
Now the compression negotiation succeeds and we can get compression working, at least from the client to the server. We have not verified that other direction works. Note that using the same code in the client before making the change to the beast implementation, this code would have triggered a runtime exception and aborted the program.  
  
### All relevant compiler information  
  
gcc (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-11-08 18:34:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2340#issuecomment-963452575  

Thank you for the detailed description.  
I would like to duplicate this locally.   
May I ask you to supply a 1-page go program to act as a test server so that I can investigate where the negotiation is breaking down?

---

## Comment 2

> Username: rpfisker  
> Created at: 2021-11-08 18:58:38 UTC  
> Updated at: 2021-11-08 19:19:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2340#issuecomment-963476504  

Hello Richard  
  
Thank you very much for your follow up!  
  
Our Go implementation is product code that I cannot share, but I’ll ask the responsible team if they could share simplified code. Alternatively, we’re using the default implementation of the compression feature in the Go websocket server. The first code block here encapsulates our setup in a much simplified program:  
  
https://github.com/gobwas/ws#compression  
  
I’ll follow up further if I could get actual code from the server team.  
  
Thank you!  
Richard  
  
  
  
Richard Palawan  
Sr. Backend Software Engineer  
[cid:IMG_3609_9333afc7-f856-4d6b-92ed-30669d6a4ce1.png].        .  
  
***@***.******@***.***>  
www.fiskerinc.com<https://www.fiskerinc.com/>  
  
  
[cid:036_sm_fb_a1347472-45be-4a0b-af83-b901fa688c57.png]<https://www.facebook.com/Fiskerinc/>  [cid:036_sm_in_468e365e-e301-4c97-a0b4-805cb9a8926e.png] <https://www.linkedin.com/company/fiskerinc/>   [cid:036_sm_twitter_ead36a19-13db-46c4-9186-b34bea9f203c.png] <https://twitter.com/FiskerInc/>   [cid:036_sm_instagram_9927e091-e056-4b30-a2b6-e681664c9634.png] <https://www.instagram.com/fiskerinc/>   [cid:App-Store-Badge_5fb0c327-dfb1-45e9-8fab-0503049885d7.png] <https://apps.apple.com/us/app/fisker-flexee/id1486002675?ls=1>   [cid:google-play-badge_61775ef0-21f3-473c-baa4-01fe66e796b3.png] <https://play.google.com/store/apps/details?id=com.fisker.fiskerapp>  
  
  
This message contains information which may be confidential and/or privileged. Unless you are the intended recipient (or authorized to receive for the intended recipient), you may not read, use, copy or disclose to anyone the message or any information contained in the message. If you have received the message in error, please advise the sender by reply e-mail and delete the message and any attachment(s) thereto without retaining any copies.  
From: Richard Hodges ***@***.***>  
Date: Monday, November 8, 2021 at 10:34 AM  
To: boostorg/beast ***@***.***>  
Cc: Richard Palawan ***@***.***>, Author ***@***.***>  
Subject: Re: [boostorg/beast] permessage-deflate client offer rejected (Issue #2340)  
CAUTION: This email originated from outside of the organization. Do not click links or open attachments unless you recognize the sender and know the content is safe.  
  
  
Thank you for the detailed description.  
I would like to duplicate this locally.  
May I ask you to supply a 1-page go program to act as a test server so that I can investigate where the negotiation is breaking down?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://m365.us.vadesecure.com/safeproxy/v4?f=-p_IbjvwwCCF3Kb8FNRMZZUZoYWuoYJRdoZKoZR6vuU17KIuOXBTbmSfIgc1vz_8&i=_0F5g_YDO6eW2zYlTFZ2LYwj_vH0mjFZ9EKWfgTAKPjF1w1nIoDA86_7_WBXx_zB3iYRv3mQfccNfgSxkrvvWw&k=lbWb&r=jdgvAJ4frW8wv7bDXZeeGTuxN1l2aJraUhVQzxDXJWB1D_1fyEYUip8ylU3F-_ff&s=934b1829865356ba81e6bbe6641c474f38d1ed31107dad252488db2f95cb17e9&u=https%3A%2F%2Fgithub.com%2Fboostorg%2Fbeast%2Fissues%2F2340%23issuecomment-963452575>, or unsubscribe<https://m365.us.vadesecure.com/safeproxy/v4?f=6bEhzxhk-vlJ0k_SsVwTVSEjLn-EMvE1QxO2MQmBUMrVp7fADA2Kc9VtVcEkX1og&i=bMLK_K_ziWaiCx8ZF43GI7PREyHZKmz0lTbVEPXIzfqZr1ojrzjY6lMuDJoUYp6qpp265j3wlzdBaD4l0tzOkA&k=rJVx&r=cNSbmezn3N7W4-cKqQ4JNMRXozysuwpikUhKjcPDAgnfM_PYp9HIow5MizsAiiI4&s=1742901e70a772fba44848d92e3359d68c76f3a1b639ccf76a761c3ce0b5fde9&u=https%3A%2F%2Fgithub.com%2Fnotifications%2Funsubscribe-auth%2FAT3E5YSNNBWCITVPZD2M3JTULAJ3JANCNFSM5HTLHDBA>.  
Triage notifications on the go with GitHub Mobile for iOS<https://m365.us.vadesecure.com/safeproxy/v4?f=mOtGt5DnX5biYe5j1RRw9ywRdhBRWWcUfajeihV9S953EMJpHD3E6MdlzW-V1wPP&i=He0n3V-KpC7hRZkW69CDNyQoMWpH7aDQIKzIlhPMuju_0ycgJZkxVYf8nB-Fx7v6SxdOboeug2XJNV8eq_OodQ&k=1VDS&r=veqUNDT0xV43M5q7b0_V1q65upilro8K73Q3AcuaFCaK3tIjw7UUfSVVNdWANyrh&s=db7cd54b9e0e7b23366eca99479528417c4c8ab8c9afca56224505347cfd6832&u=https%3A%2F%2Fapps.apple.com%2Fapp%2Fapple-store%2Fid1477376905%3Fct%3Dnotification-email%26mt%3D8%26pt%3D524675> or Android<https://m365.us.vadesecure.com/safeproxy/v4?f=23QKW5R_j8FHnzUochjfv5WiZqRmxSWYCexUhkM7h_wFUfJyKhH-aGSna1jiku_0&i=TQrb_OldhOwKxUdK8hYCWsEq1nRz7Yv_n868LFceL_5H_J-i3r-TS5QhZ9odNduAmdNrhr1Y6LufeTLh3BPsUw&k=qrRP&r=P5mMlK3u6GuD2XoTOLHCUYSV-dl6nuBZ1MCmp-rDqKOW4eDh6y3mnI-sEydk0-FE&s=a70ca026b6495a79eb54e5ddb8484a71b7d63fcec70130a0405f3ccf0b5976d0&u=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.github.android%26referrer%3Dutm_campaign%253Dnotification-email%2526utm_medium%253Demail%2526utm_source%253Dgithub>.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-11-10 20:33:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2340#issuecomment-965723674  

I have investigated this.  
The problem seems to be a non-conformance in Beast (the inability to set the window bits parameters without an `=<bits>` value) coupled with a poorly configured Go server.  
  
I replicated the problem locally by modifying the Go source code:  
  
I changed this:  
```  
	e := wsflate.Extension{  
		// We are using default parameters here since we use  
		// wsflate.{Compress,Decompress}Frame helpers below in the code.  
		// This assumes that we use standard compress/flate package as flate  
		// implementation.  
		Parameters: wsflate.DefaultParameters,  
	}  
```  
  
Which actually sets the windowbits to zero, causing the negotiation to fail on the server side when the client request of 15 bits is seen. I believe there is an error here in the Go code.  
  
To this:  
```  
	myParams := wsflate.Parameters{  
		ServerNoContextTakeover: true,  
		ClientNoContextTakeover: true,  
		ClientMaxWindowBits: wsflate.WindowBits(15),  
		ServerMaxWindowBits: wsflate.WindowBits(15),  
	}  
  
	e := wsflate.Extension{  
		// We are using default parameters here since we use  
		// wsflate.{Compress,Decompress}Frame helpers below in the code.  
		// This assumes that we use standard compress/flate package as flate  
		// implementation.  
		Parameters: myParams,  
	}  
```  
  
Which causes a match of the windowbits and successful negotiation.  
  
I will go ahead and apply a patch to Beast to allow optional setting of the WindowBits parameters. In the meantime, if you have access to the go source code, there is a another workaround in available to you in addition to patching Beast as you have done.  
  
Thanks for raising this.

---

## Comment 4

> Username: rpfisker  
> Created at: 2021-11-10 21:04:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2340#issuecomment-965744538  

Hi Richard,  
  
Thank you so much for doing this investigation! It’s great that you were able to find the root cause the correct or accommodate for the discrepancy. I’ll ask my colleagues if they can address the Go implementation defect.  
  
  
  
Richard Palawan  
Sr. Backend Software Engineer  
[cid:IMG_3609_9333afc7-f856-4d6b-92ed-30669d6a4ce1.png].        .  
  
***@***.******@***.***>  
www.fiskerinc.com<https://www.fiskerinc.com/>  
  
  
[cid:036_sm_fb_a1347472-45be-4a0b-af83-b901fa688c57.png]<https://www.facebook.com/Fiskerinc/>  [cid:036_sm_in_468e365e-e301-4c97-a0b4-805cb9a8926e.png] <https://www.linkedin.com/company/fiskerinc/>   [cid:036_sm_twitter_ead36a19-13db-46c4-9186-b34bea9f203c.png] <https://twitter.com/FiskerInc/>   [cid:036_sm_instagram_9927e091-e056-4b30-a2b6-e681664c9634.png] <https://www.instagram.com/fiskerinc/>   [cid:App-Store-Badge_5fb0c327-dfb1-45e9-8fab-0503049885d7.png] <https://apps.apple.com/us/app/fisker-flexee/id1486002675?ls=1>   [cid:google-play-badge_61775ef0-21f3-473c-baa4-01fe66e796b3.png] <https://play.google.com/store/apps/details?id=com.fisker.fiskerapp>  
  
  
This message contains information which may be confidential and/or privileged. Unless you are the intended recipient (or authorized to receive for the intended recipient), you may not read, use, copy or disclose to anyone the message or any information contained in the message. If you have received the message in error, please advise the sender by reply e-mail and delete the message and any attachment(s) thereto without retaining any copies.  
From: Richard Hodges ***@***.***>  
Date: Wednesday, November 10, 2021 at 12:33 PM  
To: boostorg/beast ***@***.***>  
Cc: Richard Palawan ***@***.***>, Author ***@***.***>  
Subject: Re: [boostorg/beast] permessage-deflate client offer rejected (Issue #2340)  
CAUTION: This email originated from outside of the organization. Do not click links or open attachments unless you recognize the sender and know the content is safe.  
  
  
I have investigated this.  
The problem seems to be a non-conformance in Beast (the inability to set the window bits parameters without an =<bits> value) coupled with a poorly configured Go server.  
  
I replicated the problem locally by modifying the Go source code:  
  
I changed this:  
  
        e := wsflate.Extension{  
  
               // We are using default parameters here since we use  
  
               // wsflate.{Compress,Decompress}Frame helpers below in the code.  
  
               // This assumes that we use standard compress/flate package as flate  
  
               // implementation.  
  
               Parameters: wsflate.DefaultParameters,  
  
        }  
  
Which actually sets the windowbits to zero, causing the negotiation to fail on the server side when the client request of 15 bits is seen. I believe there is an error here in the Go code.  
  
To this:  
  
        myParams := wsflate.Parameters{  
  
               ServerNoContextTakeover: true,  
  
               ClientNoContextTakeover: true,  
  
               ClientMaxWindowBits: wsflate.WindowBits(15),  
  
               ServerMaxWindowBits: wsflate.WindowBits(15),  
  
        }  
  
  
  
        e := wsflate.Extension{  
  
               // We are using default parameters here since we use  
  
               // wsflate.{Compress,Decompress}Frame helpers below in the code.  
  
               // This assumes that we use standard compress/flate package as flate  
  
               // implementation.  
  
               Parameters: myParams,  
  
        }  
  
Which causes a match of the windowbits and successful negotiation.  
  
I will go ahead and apply a patch to Beast to allow optional setting of the WindowBits parameters. In the meantime, if you have access to the go source code, there is a another workaround in available to you in addition to patching Beast as you have done.  
  
Thanks for raising this.  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://m365.us.vadesecure.com/safeproxy/v4?f=UTCy-8kX7Fok8-C9oS-EIdUFwBmoY0sxKbfQc7TMZZrzdCOBeDPL9jNHOxPJVMXN&i=j2Ihmfskm6mFh2hNxjF6v3svbPzPn6LjAg4PcRi0XH78t18CeCZQXuy_9aN17cAixbjqKBTG9_PirlyjVt81Zw&k=a8Cv&r=wq2n5F5CNOCWg9wsulKloLFdyj62-y6Yt05_hu1NiAkwVUzxGQ5QT12wlg5SaNis&s=0d9d4d97e724345f0f7681d01e69b4dff60065d8f2ef8e8ad3b632b80ff7e116&u=https%3A%2F%2Fgithub.com%2Fboostorg%2Fbeast%2Fissues%2F2340%23issuecomment-965723674>, or unsubscribe<https://m365.us.vadesecure.com/safeproxy/v4?f=sBrLtnwxntjVr9j-fHyjuT5jfxmSqeXCZPF4OAZImrg7AMCDR1fbHt9aw8wpy0HL&i=PoUc6xi7yp7OqAysFLDiChlXCpithWtj9Q_DBc4pTPlpnpTimZzjiuk2HCDHPTbAUHRN6lDTYqyrZ0F5uGBkRg&k=aFsG&r=xisXv4stXjMVJNQZNR2crNaAUw6ja__yoYyhrYFTWvwQ2A-7dTRhzjlp_z1U9RAf&s=cec32cce62e92323633cf1106d83ca66f4eb28fb929ae8c8721aaf7295c261b0&u=https%3A%2F%2Fgithub.com%2Fnotifications%2Funsubscribe-auth%2FAT3E5YU3ROSC2LX6VXJ2VDDULLJJHANCNFSM5HTLHDBA>.  
Triage notifications on the go with GitHub Mobile for iOS<https://m365.us.vadesecure.com/safeproxy/v4?f=mFBsj1h8YbS1evq1lQSyJA4nNmZHrblhxSf7imbG149jQxEt_sTJbeFf74-hhy1x&i=VKQrjiNMiO4lRb41PpxRxUvCg28zQYG6cO5rdRit61c2xwY_BCZDzumKFQlH-L7C8L07JPExSmjKG0z9RkDo7w&k=r6HO&r=1wqGcntT4-eWbDDTr0-vyhUOVErDqcnYsyu2pnmtrSYg-Vpy1JkFv7-yo5SjDQGS&s=c6827e374b9f6a48a3ef558626bdb37928228b24f3826d2a1e09f42e6701384f&u=https%3A%2F%2Fapps.apple.com%2Fapp%2Fapple-store%2Fid1477376905%3Fct%3Dnotification-email%26mt%3D8%26pt%3D524675> or Android<https://m365.us.vadesecure.com/safeproxy/v4?f=UyVkwBN-K3F7Y_H_ukh_S9-dnUWwCkAEBHpz7Y9rMWyEq0itdMfkq4ZequtD9Z29&i=IBpnnBne5WxgfsDAe4xjrjdoghFs1acu-49HG0BJBk4aIv6dVRD7VDiR-r_xdzVG7i-q31s_-yZjejSFUVfm2A&k=UaRv&r=X1SvhQ__DWmKUUIhsWLsnzgFUfOdYcE3F2sP9i5If81vJyTf9qNjRQtdgAZJyoy0&s=06b899b85455ca993634d388fd4c1be897bc4914a456cfc749b62bd943e8c86d&u=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.github.android%26referrer%3Dutm_campaign%253Dnotification-email%2526utm_medium%253Demail%2526utm_source%253Dgithub>.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2340#issuecomment-1008220695  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-06-18 19:43:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2340#issuecomment-1159547810  

@rpfisker, is this resolved?
