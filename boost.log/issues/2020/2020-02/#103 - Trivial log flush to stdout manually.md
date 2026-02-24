# #103 - Trivial log flush to stdout manually [Closed]

> Username: daniel-emotech  
> Created at: 2020-02-26 16:09:26 UTC  
> Updated at: 2020-02-29 13:04:47 UTC  
> Closed at: 2020-02-29 13:04:47 UTC  
> Url: https://github.com/boostorg/log/issues/103  

I've got a grpc server using boost asio and I've just set up boost log as the logging solution using `BOOST_LOG_TRIVIAL` to do my printed logs. But I noticed the logs at the end of a request aren't being printed until the next request comes in when testing sequentially. I was just wondering if there was a way of manually flushing any queued up messages to make my log based debugging easier?

---

## Comment 1

> Username: Lastique  
> Created at: 2020-02-26 19:20:16 UTC  
> Updated at: 2020-02-26 19:28:17 UTC  
> Url: https://github.com/boostorg/log/issues/103#issuecomment-591597358  

Currently, the only way to automatically flush the output is to explicitly register a console sink (e.g. by calling [`add_console_log`](https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/log/detailed/utilities.html#log.detailed.utilities.setup.convenience)) and enable auto-flush on it. `add_console_log` returns a pointer to the newly added sink, you can use it to enable auto-flush like this:  
  
```  
auto psink = logging::add_console_log();  
psink->locked_backend()->auto_flush(true);  
```  
  
You will also have to set the formatter on the sink and add common attributes to get timestamps in log records. You can keep using `BOOST_LOG_TRIVIAL` though, the log records will just get routed to the newly added sink instead of the default sink. You can also flush all sinks manually by calling `flush` on the logging core.  
  
I probably should add an API to enable auto-flushing in the default sink. Also, `core::flush` should work on the default sink, too, but it currently doesn't. I'll keep this bug as a reminder.

---

## Comment 2

> Username: daniel-emotech  
> Created at: 2020-02-27 14:24:53 UTC  
> Url: https://github.com/boostorg/log/issues/103#issuecomment-591998557  

Ah cool that sorted it :+1:
