# #2013 - Async websocket creates a large binary & object file [Open]

> Username: amnuwan  
> Created at: 2020-07-11 10:41:36 UTC  
> Updated at: 2024-10-15 09:49:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2013  

**Version of Boost 1.70 ( 1.73 too )**  
  
Steps necessary to reproduce the problem  
--------------  
  
Build sample async ssl websocket example   
  
https://www.boost.org/doc/libs/1_70_0/libs/beast/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp  
  
On Mac OS with clang,  
This will produce a **release binary 6.3 MB**. This is not due to OpenSSL. You will see that **object file ( websocket_client_async_ssl.o ) is also 3.8 MB.**  
  
And following statement contributes to shocking **3.1 MB** of it.   
  
// Perform the websocket handshake  
        ws_.async_handshake(host_, "/",  
            beast::bind_front_handler(  
                &session::on_handshake,  
                shared_from_this()));  
  
while sync version of same ( ws_.handshake ), contributes only 400 KB extra.  
  
File size changes as below.  
----------  
1. Async ws-handshake - ( obj: 3.8 MB,  exe: 6.3 MB )  
2. No ws-handshake - ( obj: 0.5 MB,  exe: 2.9 MB )  
3. Sync ws-handshake - ( obj: 0.9 MB,  exe: 3.2 MB )  
  
SSL present in all of above case.  
  
I checked some cases with latest boost version 1.73 also, no noticeable change in reduction. Probably it takes more.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-07-11 11:48:15 UTC  
> Updated at: 2020-07-11 14:25:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-657051853  

Debug build or release? If release build, did you strip debugging symbols?  
  
On Sat, 11 Jul 2020 at 12:41, amnuwan <notifications@github.com> wrote:  
  
> Version of Boost 1.70 ( 1.73 too )  
>  
> Steps necessary to reproduce the problem  
>  
> Build sample async ssl websocket example  
>  
>  
> https://www.boost.org/doc/libs/1_70_0/libs/beast/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp  
>  
> On Mac OS , clang  
>  
> This will produce a release binary bigger than 2.5 MB. This is not due to  
> OpenSSL. You will see that object file ( websocket_client_async_ssl.o ) is  
> also around 2 MB.  
>  
> And following statement contributes to around 1.6 MB of it.  
>  
> // Perform the websocket handshake  
> ws_.async_handshake(host_, "/",  
> beast::bind_front_handler(  
> &session::on_handshake,  
> shared_from_this()));  
>  
> Striping reduces the size little bit ( like 200 KB ).  
>  
> However sync version is not this big. If you replace above statement with  
> ws_.handshake size get smaller by around 1 MB. Making total object size  
> around 1 MB.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2013>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSIAXXAJAA7YC6KF623R3A6W7ANCNFSM4OXH3D6A>  
> .  
>  
--

---

## Comment 2

> Username: amnuwan  
> Created at: 2020-07-11 17:14:55 UTC  
> Updated at: 2020-07-12 05:41:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-657097603  

> Debug build or release? If release build, did you strip debugging symbols?  
  
Release build.  
   
Stripping ( compiling with option -S ) increases object file further : from 3.8 MB to 23 MB  
Anyway I'm not able to link after stripping.   
  
No luck with striping for me but better if we can find the **reason for this bloat specially in async mode**.   
  
Further with respect to striping, additional symbols can help to debug a crash I suppose, which could be the reason they are in a release build, right ?

---

## Comment 3

> Username: djarek  
> Created at: 2020-07-11 18:31:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-657108359  

@amnuwan That's the wrong flag, `-S` does the following:  
> Stop after the stage of compilation proper; do not assemble. The output is in the form of an assembler code file for each non-assembler input file specified.   
  
The flag for stripping is `-s`.  
  
As for the bloat- it's most likely related to how the async functions of websocket stream cause multiple instantiations of the async algorithms of the underlying stream, because multiple buffer types are passed.

---

## Comment 4

> Username: amnuwan  
> Created at: 2020-07-12 05:56:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-657179262  

@djarek thanks, I'll check about the option, I know striping can reduce the size a little, but if it has a cost to crash error reporting, I don't preffer that.  
  
single statement cause 3.1 MB size increase, making beast is a difficult option for a mobile or embeded library overall. I'm developing a communication library, can't sacrifice around 5 MB of size just for websocket connectivity.  
  
@djarek Mentioned template instantiation, how many templates would it require increase the size to this level by a single line of code. If this is unavoidable, then something is not right with the design of asio/beast. It lead us to consider, beast as a heavy library to perform websocket connectivity.

---

## Comment 5

> Username: djarek  
> Created at: 2020-07-13 01:32:33 UTC  
> Updated at: 2020-07-13 01:32:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-657313525  

Some things that may help:  
- don't use polymorphic executor (the default) on I/O objects, prefer the exact type (usually `io_context::executor_type`)  
- try to use the same buffer type in all async calls  
- if you're not using the timeouts/rate limiting features of  `beast::tcp_stream` , don't use it, use a socket type from ASIO instead  
- enable LTO (not really related to beast, but in general), which enables more optimization opportunities  
- make sure you optimize for size (-Os), if size is important.

---

## Comment 6

> Username: stale[bot]  
> Created at: 2020-08-16 07:19:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-674490938  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: amnuwan  
> Created at: 2020-08-16 07:29:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-674491876  

No. The issue remains. 1st message has details to reproduce.  
  
On Sun, 16 Aug 2020 at 3:19 PM, stale[bot] <notifications@github.com> wrote:  
  
>  
>  
> This issue has been open for a while with no activity, has it been  
> resolved?  
>  
>  
>  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2013#issuecomment-674490938>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AGCND2G7HLKEOCG73SUF5MDSA6CADANCNFSM4OXH3D6A>  
> .  
>  
>  
> --  
____________________________  
A.M Nuwan Abeysinghe  
Mobile : +94 71 5800808  
T.P     : +94 11 2617273

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-08-16 08:09:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-674495860  

Ok, I’ve been off work for a few days. I’ll tackle this on Monday.  
  
Thanks for your patience.

---

## Comment 9

> Username: amnuwan  
> Created at: 2020-08-23 07:09:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-678738543  

Any update on this ?  
  
  
On Sun, Aug 16, 2020, 16:09 Richard Hodges <notifications@github.com> wrote:  
  
> Ok, I’ve been off work for a few days. I’ll tackle this on Monday.  
>  
> Thanks for your patience.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2013#issuecomment-674495860>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AGCND2CNF3S5RGQQNMGIJLLSA6H37ANCNFSM4OXH3D6A>  
> .  
>

---

## Comment 10

> Username: madmongo1  
> Created at: 2020-08-23 08:41:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-678746916  

Yes, we've been in discussion on how we can reduce template type expansions for internal completion handlers with little impact on performance.  
  
We have a plan, and work is under way.

---

## Comment 11

> Username: amnuwan  
> Created at: 2020-08-24 13:49:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-679137714  

Nice, so I will continue to use beast for mobile assuming size will be reduced in the recent future.  
  
Below library generate a really small binary I was thinking use it if beast couldn't fix this.  
  
https://machinezone.github.io/IXWebSocket/  
  
Thanks !

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-08-24 14:45:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-679170882  

great. we're on it.

---

## Comment 13

> Username: stale[bot]  
> Created at: 2020-10-04 03:01:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-703193597  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 14

> Username: stale[bot]  
> Created at: 2020-10-12 00:32:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-706795743  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 15

> Username: amnuwan  
> Created at: 2020-10-13 08:50:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-707594393  

Can this take some time to be resolved ? Like 2-4 months ?

---

## Comment 16

> Username: madmongo1  
> Created at: 2020-10-13 08:53:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-707596112  

It can, depending on what else is distracting the maintainer :)  
  
PRs are always welcome if you'd like to help move the process along.

---

## Comment 17

> Username: stale[bot]  
> Created at: 2020-11-16 03:45:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-727717640  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 18

> Username: stale[bot]  
> Created at: 2020-12-25 12:13:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-751240985  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 19

> Username: amnuwan  
> Created at: 2021-08-29 21:00:05 UTC  
> Updated at: 2021-08-29 21:00:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-907871648  

As a mitigation, following things helped  
  
-fvisibility=hidden -Os -flto=full  
-Wl,--gc-sections,--as-needed  
Also striping  
  
In my project, overall I was able to obtain .so file of size 3.1 MB ( Around 2 MB goes to beast ).   
  
Object file correspond to beast connection with above line is still 4.3 MB. It's a small 350 lines class which has nothing else but beast websocket connection with SSL.  
  
This problem is severe only when it comes to android mobile applications. It will be really helpful for others if we put some warning at main page. Like below,  
  
**Warning :** Websocket code with SSL contributes to additional ~1-2 MB release binary size when compiled as ".so" library.

---

## Comment 20

> Username: dfranusic  
> Created at: 2022-04-07 10:01:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-1091468684  

I am using Boost.Beast to implement a Websocket SSL server on a range of embedded devices. Although "losing" around two megabytes of free space is not a big issue, it would be great if the resulting binary (or object file) could be trimmed down a bit.

---

## Comment 21

> Username: pfeatherstone  
> Created at: 2024-10-14 13:05:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-2411179849  

I've noticed massive binary sizes too. I'm also using Boost.Json for parsing/writing HTTP requests/response. A very simple HTTP + Websocket over SSL application with some JSON parsing results in a 7MB binary with everything statically linked. It's massive. I'm also putting this on an embedded device but i can get away with it. I've noticed this a lot with Boost libraries. The compile times and binary sizes are massive. Fantastic libraries but what is it about Boost and bloated bins.

---

## Comment 22

> Username: ashtum  
> Created at: 2024-10-14 13:11:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-2411230200  

> I've noticed massive binary sizes too. I'm also using Boost.Json for parsing/writing HTTP requests/response. A very simple HTTP + Websocket over SSL application with some JSON parsing results in a 7MB binary with everything statically linked. It's massive. I'm also putting this on an embedded device but i can get away with it. I've noticed this a lot with Boost libraries. The compile times and binary sizes are massive. Fantastic libraries but what is it about Boost and bloated bins.  
  
Are you sure you're building in release mode? Based on what you've explained, a binary size of 1 to 2 MB is expected.

---

## Comment 23

> Username: pfeatherstone  
> Created at: 2024-10-14 13:14:13 UTC  
> Updated at: 2024-10-14 13:15:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-2411235983  

I'm building with `-O3 -ffast-math -s`.  
In debug mode, I get a binary of size 47MB.  
I've also got a CLI using Boost.program_options. I forgot to mention that. But how much is that adding?

---

## Comment 24

> Username: ashtum  
> Created at: 2024-10-14 13:25:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-2411270493  

Unfortunately, there isn’t much you can do about it. Both Asio and Beast are heavily templated, and even small changes to message types result in the re-instantiation of containers and operations. If you have many message types, you might want to give [http::message_generator](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__message_generator.html) a try, but I don't expect it to make a huge difference.

---

## Comment 25

> Username: pfeatherstone  
> Created at: 2024-10-14 13:27:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-2411275442  

OK, I guess that's the nature of heavily templated libraries. There is no such thing as a zero-cost abstraction.

---

## Comment 26

> Username: amnuwan  
> Created at: 2024-10-15 09:49:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2013#issuecomment-2413419193  

Something is wrong if the size increase is this big for a websocket  
implementation over existing asio socket layer. I understand the  
unavoidable size difference for TLS implementation but there is a  
significant increase on top of it.  
  
Also make sure to try related “strip” commands post release build.  
Sometimes it reduces the size by like 60%.  
  
On Mon, 14 Oct 2024 at 21:27, pfeatherstone ***@***.***>  
wrote:  
  
> OK, I guess that's the nature of heavily templated libraries. There is no  
> such thing as a zero-cost abstraction.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2013#issuecomment-2411275442>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AGCND2AUCTESUFSV6M7YI53Z3PBFTAVCNFSM6AAAAABP463PUSVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDIMJRGI3TKNBUGI>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>
