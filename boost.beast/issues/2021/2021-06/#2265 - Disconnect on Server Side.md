# #2265 - Disconnect on Server Side [Closed]

> Username: treyweaver  
> Created at: 2021-06-21 17:25:15 UTC  
> Updated at: 2024-01-01 09:11:32 UTC  
> Closed at: 2024-01-01 09:11:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2265  

I am using version 1.76 as a client.  
  
Ok, this is a hard one.  
  
I am trying to work with an existing web socket (zellowork.io).  But, I cannot stay connected to them.  I put wireshark on the connection and I can clearly see that it is their side that is providing the disconnect [FIN, ACK].    Their product was designed so the web socket is always open and you send data when you need to.  But I am finding that after some time they disconnect it can be as short as 10min and sometimes 6.5hrs.  
  
Normally I would say the issue is on their side but they say they have had this product running for a few years and they have no problems with their Android, IOS apps on the mobile side; and no issues using JavaScript, Go or Qt on the desktop side.  
  
I tried sending data every 5 minutes as a sort of keep alive, but it did not help.  When it disconnects it does not appear to be a response to anything that I have sent them.  Because it can disconnect well after my last data transfer.  
  
I can see on wireshark the underlying websocket keep alives every 30 seconds.  
  
That is really all the data I have.  Are there any parameters that I should change to maybe resolve this issue.  Right now I have timeouts set the Default::Client.  Anything I should tell zello to look for?  
  
Thanks,

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-06-21 17:40:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2265#issuecomment-865222436  

`timeout::suggested(client)` will instruct your client to not initiate pings. However it will respond to them:  
  
```  
        static  
        timeout  
        suggested(role_type role) noexcept  
        {  
            timeout opt{};  
            switch(role)  
            {  
            case role_type::client:  
                opt.handshake_timeout = std::chrono::seconds(30);  
                opt.idle_timeout = none();  
                opt.keep_alive_pings = false;  
                break;  
```  
  
However, if you want the client to initiate keepalive pings and disconnect if not answered, the easiest way to do this is to use `timeout::suggested(server)`:  
  
```  
            case role_type::server:  
                opt.handshake_timeout = std::chrono::seconds(30);  
                opt.idle_timeout = std::chrono::seconds(300);  
                opt.keep_alive_pings = true;  
                break;  
```  
  
So you might try setting the option before initiating your handshake, like this:  
  
```  
        ws.set_option(  
            beast::websocket::stream_base::timeout::suggested(  
                beast::role_type::client));  
  
```  
  
where in this case `ws` is your instance of `beast::websocket::stream`  
  
The ping-pong message pump is driven by your calls to `async_read`, so there is no need to take any further action beyond reading messages as you normally do.  
  
Other than that I'm not sure what to suggest. If you are willing to post a test program that demonstrates the issue I'm happy to take a look my end.  
(please make it short - one self-contained cpp file)

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-06-25 07:48:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2265#issuecomment-868297022  

Hi @treyweaver, were you able to make progress or still stuck?

---

## Comment 3

> Username: treyweaver  
> Created at: 2021-06-25 18:13:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2265#issuecomment-868746532  

Richard,  
  
I am still suck, but that is because I have been home sick.  I finally managed to make some changes to the code today and I am testing the results.  Should know by tomorrow if the changes I made helped.  
  
Trey  
  
On Fri, Jun 25, 2021, at 3:49 AM, Richard Hodges wrote:  
>   
>   
> Hi @treyweaver <https://github.com/treyweaver>, were you able to make progress or still stuck?  
>   
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/beast/issues/2265#issuecomment-868297022>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/ABJBASOY4FIBSDS4YAP5LPDTUQYG7ANCNFSM47CAC4LA>.  
>

---

## Comment 4

> Username: stale[bot]  
> Created at: 2022-01-09 03:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2265#issuecomment-1008220777  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:12:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2265#issuecomment-1256863318  

@treyweaver did you make any progress?
