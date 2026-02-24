# #69 - Can't append file using text_file_backend [Closed]

> Username: ercdude  
> Created at: 2019-01-05 12:44:00 UTC  
> Updated at: 2019-01-05 20:09:06 UTC  
> Closed at: 2019-01-05 13:02:10 UTC  
> Url: https://github.com/boostorg/log/issues/69  

Hi! I was trying to use the boost log to write into a file and append to it when the application restarts (and not to create a new rotating file) with a basic `text_file_backend` like the following, but it seems that it doesnt work - which means that nothing changes when `boostlog_keywords::open_mode = std::ios_base::app` is added.  
  
```    
  typedef boostlog_sinks::synchronous_sink< boostlog_sinks::text_file_backend > file_sink;  
  boost::shared_ptr< file_sink > sink(new file_sink(  
      boostlog_keywords::auto_flush = true,  
      boostlog_keywords::auto_flush = true,  
      boostlog_keywords::file_name = "file-%m%d%Y_%H%M_%2N.log",  
      boostlog_keywords::rotation_size = 5 * 1024 * 1024,  
      boostlog_keywords::format = "[%TimeStamp%][%ThreadID%] *%Severity%* %Message%",  
      boostlog_keywords::open_mode = std::ios_base::app  
  ));  
```  
  
  
The full code is [here](https://wandbox.org/permlink/KU9xNX1xRMzVeDKp).

---

## Comment 1

> Username: Lastique  
> Created at: 2019-01-05 13:02:10 UTC  
> Url: https://github.com/boostorg/log/issues/69#issuecomment-451653677  

Appending will only work when the generated file name matches an existing file. This means that file names with timestamps will not work well with appending.

---

## Comment 2

> Username: ercdude  
> Created at: 2019-01-05 13:08:20 UTC  
> Updated at: 2019-01-05 13:09:29 UTC  
> Url: https://github.com/boostorg/log/issues/69#issuecomment-451654025  

even if it's in the same time? In the example, if I open the application twice in the same minute, two file logs (`file-mmddYY_HHMM_00.log` and `file-mmddYY_HHMM_01.log`) will be created and the file name is the same.   
  
Or you mean that if using time (any, even if it's just the year) the append is ignored?

---

## Comment 3

> Username: Lastique  
> Created at: 2019-01-05 13:22:51 UTC  
> Url: https://github.com/boostorg/log/issues/69#issuecomment-451654958  

> even if it's in the same time?  
  
It's the file names what matters. In your example, the timestamps do match, but the counter value doesn't.  
  
The counter value doesn't match because you call `scan_for_files`, which by default updates the starting counter value the sink uses to generate the file name. That call is intended to be used with a file collector, so that the older rotated log files are tracked. You're not using one.

---

## Comment 4

> Username: ercdude  
> Created at: 2019-01-05 15:00:06 UTC  
> Url: https://github.com/boostorg/log/issues/69#issuecomment-451662750  

but if I remove the `scan_for_files`, new file still will be created. But instead of `xxx00.log` it will be `xxx.log000`

---

## Comment 5

> Username: ercdude  
> Created at: 2019-01-05 15:05:01 UTC  
> Url: https://github.com/boostorg/log/issues/69#issuecomment-451663075  

oooh, okay... I have to *remove* the file collector. So it's impossible to append _and_ collect at the same time, is that it?

---

## Comment 6

> Username: Lastique  
> Created at: 2019-01-05 15:54:24 UTC  
> Updated at: 2019-01-05 15:54:51 UTC  
> Url: https://github.com/boostorg/log/issues/69#issuecomment-451666605  

Yes, given that the file name is generated only once, there's no practical way to ensure appending happens while file collecting is enabled. Either appending won't happen (because of differing file names) or there will be name clashes when the file is collected. I'm working on a solution, though, so hopefully this problem will be resolved.

---

## Comment 7

> Username: ercdude  
> Created at: 2019-01-05 15:59:51 UTC  
> Url: https://github.com/boostorg/log/issues/69#issuecomment-451667060  

Okay, thanks!  
  
Maybe a disclaimer [here](https://www.boost.org/doc/libs/1_57_0/libs/log/doc/html/log/detailed/sink_backends.html#log.detailed.sink_backends.text_file.managing_rotated_files) would be nice. :) I don't know if/how I can upload a documentation ""fix"", otherwise I would add

---

## Comment 8

> Username: Lastique  
> Created at: 2019-01-05 18:58:54 UTC  
> Updated at: 2019-01-05 19:01:01 UTC  
> Url: https://github.com/boostorg/log/issues/69#issuecomment-451681867  

If you're interested, you can try if https://github.com/boostorg/log/commit/e823f884679e8cfb949a36a17a4516cd72d7feca helps. With it, you can modify your initialization code like this:  
  
```  
typedef boostlog_sinks::synchronous_sink< boostlog_sinks::text_file_backend > file_sink;  
boost::shared_ptr< file_sink > sink(new file_sink(  
      boostlog_keywords::auto_flush = true,  
      boostlog_keywords::file_name = "file.log",  
      boostlog_keywords::target_file_name = "file-%m%d%Y_%H%M_%2N.log",  
      boostlog_keywords::rotation_size = 5 * 1024 * 1024,  
      boostlog_keywords::format = "[%TimeStamp%][%ThreadID%] *%Severity%* %Message%",  
      boostlog_keywords::open_mode = std::ios_base::app  
));  
```

---

## Comment 9

> Username: ercdude  
> Created at: 2019-01-05 20:09:06 UTC  
> Url: https://github.com/boostorg/log/issues/69#issuecomment-451686711  

Nice, I'll check it out asap. Thanks!
