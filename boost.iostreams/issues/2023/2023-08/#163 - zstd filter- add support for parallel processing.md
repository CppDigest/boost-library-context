# #163 - zstd filter: add support for parallel processing [Open]

> Username: marxin  
> Created at: 2023-08-30 06:48:33 UTC  
> Updated at: 2023-09-13 10:16:58 UTC  
> Url: https://github.com/boostorg/iostreams/issues/163  

As you probably know, `zstd` is famous for its fast (and stable) parallel compression capability. It would be great if `iostream` would teach the skill.  
  
I've got a WIP parallel implementation of the `io::multichar_output_filter` filter:  
[boost-zstd.cc.txt](https://github.com/boostorg/iostreams/files/12472545/boost-zstd.cc.txt)

---

## Comment 1

> Username: marxin  
> Created at: 2023-08-30 06:48:44 UTC  
> Url: https://github.com/boostorg/iostreams/issues/163#issuecomment-1698593230  

@rdoeffinger

---

## Comment 2

> Username: rdoeffinger  
> Created at: 2023-08-30 07:28:30 UTC  
> Url: https://github.com/boostorg/iostreams/issues/163#issuecomment-1698640778  

There are 3 main stumbling blocks:  
- it seems there is no API designed for how to set filter options in a generic way. While it's possible to just add a function for setting multithreading specifically, it would be better if it could support more, like setting compression levels in a standard way for all compression filters, allowing to set any new options that are added to zstd etc. Designing this is not easy  
- it seems zstd does not have an option for "choose a good amount of worker threads", it only accepts an explicit number. That makes it a pain to use and hard to provide a nice "enable multithreading" option  
- I don't have a use-case that benefits much from this, so I would be in no position to test if it works well and if there are any performance bottlenecks (e.g. too much flushing being added by default).

---

## Comment 3

> Username: marxin  
> Created at: 2023-08-30 07:59:47 UTC  
> Url: https://github.com/boostorg/iostreams/issues/163#issuecomment-1698682695  

> * it seems zstd does not have an option for "choose a good amount of worker threads", it only accepts an explicit number. That makes it a pain to use and hard to provide a nice "enable multithreading" option  
  
Yeah, one might want to use the auto-detection capability of the library:  
`--auto-threads={physical,logical} (default: physical): When using a default amount of threads via -T0, choose the default based on the number of detected physical or logical cores.`  
  
> * I don't have a use-case that benefits much from this, so I would be in no position to test if it works well and if there are any performance bottlenecks (e.g. too much flushing being added by default).  
  
Heh. I think every time you compress something bigger you want to have it done as fast as possible. That's why using `gzip` won't work as it's only single-threaded compression algorithm.

---

## Comment 4

> Username: rdoeffinger  
> Created at: 2023-08-30 17:30:35 UTC  
> Updated at: 2023-08-30 17:30:51 UTC  
> Url: https://github.com/boostorg/iostreams/issues/163#issuecomment-1699578196  

> Yeah, one might want to use the auto-detection capability of the library  
  
My understanding is that the library does not have this capability, only the binary. But if there is a library API to select this mode it would indeed be helpful.  
  
> Heh. I think every time you compress something bigger you want to have it done as fast as possible.  
  
Not if the thing producing the data is producing it at a fairly leisurely pace. It just runs for long enough that you need compression. Also all cases where you have a lot of different things to compress are better handled by parallelism across the items to compress, not by parallelizing the compression.  
But anyway, not the main point and not an argument against the feature, just that this complicates developing and testing such a feature for me (plus, reduces motivation to do so).

---

## Comment 5

> Username: marxin  
> Created at: 2023-08-31 09:45:52 UTC  
> Url: https://github.com/boostorg/iostreams/issues/163#issuecomment-1700715741  

Sure, I understand it's not an exciting use case for you. In my case, I stream data to a single file where I would like to apply compression. And that's why boost IO streams come in handy and it would help me to save a lot of time.

---

## Comment 6

> Username: rdoeffinger  
> Created at: 2023-09-11 17:55:48 UTC  
> Url: https://github.com/boostorg/iostreams/issues/163#issuecomment-1714337993  

I had a bit of a look.  
The good news: it's easy to add support for extra parameters via the zstd_params struct.  
The bad news: the library functions to set parallel compression are only available from version 1.4.  
So would need to figure out how to handle that.  
While it's possible to automatically use these new functions when the header is 1.4 or newer, that would mean that a binary built against 1.4 will now no longer run on a system that has 1.3, whereas it would have worked fine before.  
Not sure if that is really a problem, but it's a risk.  
Then there is also the question, what to do if mutithreading was requested but turns out to not be available?  
There is the more ugly and hard-core option to just give access to the cstream_ and dstream_ variables, leaving it to the user to figure it out and set these options, but that seems like bad design.

---

## Comment 7

> Username: marxin  
> Created at: 2023-09-13 10:16:58 UTC  
> Url: https://github.com/boostorg/iostreams/issues/163#issuecomment-1717349418  

Well, ZSTD `1.4` is more than 4 years old, so I would not spent much time with older ZSTD releases.  
And yes, if MT is not available (or disabled in libzstd), then single-threaded mode seems to me like a reasonable fallback.
