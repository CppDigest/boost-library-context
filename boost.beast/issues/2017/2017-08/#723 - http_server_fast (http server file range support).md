# #723 - http_server_fast (http server file range support) [Open]

> Username: maytrue  
> Created at: 2017-08-07 09:53:04 UTC  
> Updated at: 2024-01-24 00:03:10 UTC  
> Url: https://github.com/boostorg/beast/issues/723  

I modify http_server_fast demo, run  as http server  and return media data from file to vlc player.  
  
I parse "Range: bytes=" to support range, and player can play well, but stop when seek to end.  
  
http_server_fast stop at   
![screen shot 2017-08-07 at 4 26 36 pm](https://user-images.githubusercontent.com/1849557/29021768-343ebc82-7b99-11e7-8c86-0bf6624fc00e.png)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-07 12:52:30 UTC  
> Url: https://github.com/boostorg/beast/issues/723#issuecomment-320655107  

The `file_body` does not currently support serving a range, but I have done some of that work already. I can add a function which lets you specify the range you want to serve. I was thinking something like this:  
```  
template<class File>  
class basic_file_body<File>::value_type  
{  
    ...  
    /// Set the range of bytes to deliver  
    void range(std::size_t first, std::size_t last);  
```  
  
To work around your issue, try this instead:  
```  
if(nread == 0)  
    return boost::none;  
```  
  
Thanks

---

## Comment 2

> Username: maytrue  
> Created at: 2017-08-09 02:05:41 UTC  
> Url: https://github.com/boostorg/beast/issues/723#issuecomment-321131622  

It  works, thanks

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-08-09 02:11:04 UTC  
> Url: https://github.com/boostorg/beast/issues/723#issuecomment-321132420  

I'll keep this open since I do plan to add built-in support for "range" on `file_body`.

---

## Comment 4

> Username: ecorm  
> Created at: 2024-01-08 23:25:49 UTC  
> Url: https://github.com/boostorg/beast/issues/723#issuecomment-1881978529  

> I'll keep this open since I do plan to add built-in support for "range" on file_body.  
  
Was range support for `file_body` ever implemented? I'm scouring through the documentation and source code and can't find such support.

---

## Comment 5

> Username: ecorm  
> Created at: 2024-01-08 23:48:02 UTC  
> Url: https://github.com/boostorg/beast/issues/723#issuecomment-1881997351  

I should add that I don't really need this range request support right now; I was just curious to know if it was supported.

---

## Comment 6

> Username: ashtum  
> Created at: 2024-01-09 06:45:39 UTC  
> Updated at: 2024-01-09 06:49:33 UTC  
> Url: https://github.com/boostorg/beast/issues/723#issuecomment-1882497017  

@ecorm No, there is no support for specifying a range in basic_file_body. However we can add this if you have a use case for it.

---

## Comment 7

> Username: ecorm  
> Created at: 2024-01-09 13:11:37 UTC  
> Url: https://github.com/boostorg/beast/issues/723#issuecomment-1883036739  

> @ecorm No, there is no support for specifying a range in basic_file_body. However we can add this if you have a use case for it.  
  
The use case would obviously be video streaming: https://developer.mozilla.org/en-US/docs/Web/Media/Audio_and_video_delivery/Setting_up_adaptive_streaming_media_sources  
  
I currently don't have a need for video streaming in my project, but I'm surprised range support hasn't been requested more by others.  
  
A Beast-provided way of parsing the Range field would go nicely with file_body ranges if the latter were to be implemented.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2024-01-23 23:50:18 UTC  
> Url: https://github.com/boostorg/beast/issues/723#issuecomment-1907105897  

> The use case would obviously be video streaming  
  
Right, but are people using Beast to build video streamers? I doubt it, as there are already commercial solutions which Beast cannot hope to compete with. What I mean when I say use-case, is the specific motivating example which informs us on how to proceed (going in order of least design work to most design work):  
  
1. Define this as "responsibility of the user",  
2. Provide a working example using best practices upon which users can build, or  
3. Improve the Beast public API to support this  
  
> A Beast-provided way of parsing the Range field  
  
Yes, I like this a lot! The design work is not difficult at all and it can't possibly affect existing programs. We could start with an example, get some feedback from users, and then promote it into a public API.  
  
Thanks

---

## Comment 9

> Username: ecorm  
> Created at: 2024-01-24 00:03:09 UTC  
> Url: https://github.com/boostorg/beast/issues/723#issuecomment-1907118528  

I'm not an expert in this area, but I imagine a video streaming server might want to do something more sophisticated than opening a file and read a part of it for every Range request that comes in. One might want to read ahead and cache parts of the file in memory in anticipation of the next Range requests that will soon come in.  
  
I'll re-iterate that I don't have a requirement for video streaming in my project. I was just curious about the status of this issue.
