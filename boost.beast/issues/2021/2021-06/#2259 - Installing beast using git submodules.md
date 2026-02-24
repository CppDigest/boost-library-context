# #2259 - [help] Installing beast using git submodules [Closed]

> Username: Nitwel  
> Created at: 2021-06-14 11:27:37 UTC  
> Updated at: 2022-09-24 05:15:08 UTC  
> Closed at: 2022-09-24 05:15:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2259  

Hi, I'm really new to c++ and trying to install and use beast by adding it to my project as a git submodule.  
My problem is that VisualStudio only finds `boost::beast` but not `boost::beast::http` from this example: https://github.com/boostorg/beast/blob/710cc53331f197f6f17e8c38454c09df68e43c03/example/websocket/server/sync/websocket_server_sync.cpp#L16-L29  
  
I've included the hpp files like so:  
  
![grafik](https://user-images.githubusercontent.com/22577866/121884817-ac140d00-cd13-11eb-92cb-beac032bd3d1.png)  
  
where `/libraries/beast` is the beast submodule and same goes for asio and code.  
  
The error VS gives me:  
![grafik](https://user-images.githubusercontent.com/22577866/121885081-0614d280-cd14-11eb-9d3b-15d562f939f3.png)  
  
What exactly am I doing wrong that VS does find `boost::beast` but not `boost::beast::http`?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-06-14 11:49:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2259#issuecomment-860622428  

Boost (including beast) should be installed either the supplied b2 tool.  
The Cmake script is there for maintenance use at the moment.  
Cmake support for installing boost is in the pipeline but currently not part of the supported user experience.  
@pdimov

---

## Comment 2

> Username: pdimov  
> Created at: 2021-06-14 12:04:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2259#issuecomment-860630345  

You haven't included `<boost/beast/http.hpp>`.

---

## Comment 3

> Username: Nitwel  
> Created at: 2021-06-14 12:32:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2259#issuecomment-860647128  

Including `<boost/beast/http.hpp>` still gives an error.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2022-01-09 03:17:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2259#issuecomment-1008220783  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:15:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2259#issuecomment-1256863775  

@Nitwel we'd need the error to help you. Since it's been over a year, I assume you figured it out by now. If not please reopen the issue.
