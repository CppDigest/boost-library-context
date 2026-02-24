# #81 - Send multiple message at a time using one logger statement [Closed]

> Username: mrunmayi29  
> Created at: 2019-04-08 12:16:43 UTC  
> Updated at: 2019-04-09 18:18:14 UTC  
> Closed at: 2019-04-08 13:13:21 UTC  
> Url: https://github.com/boostorg/log/issues/81  

Can I send multiple logs in one logging statement? I have 1000 logs in a vector, instead of sending them one by one can I send them together?  
  
```  
BOOST_LOG_SEV(lg, sinks::syslog::alert) << message ;  
```  
This statement will send 1 message at a time. If I have a vector of strings, can I send all the strings in vector using only 1 BOOST_LOG_SEV ? Do I need to modify formatter of sink?  
  
```  
              sink = boost::shared_ptr<sinks::synchronous_sink< sinks::syslog_backend >>(new sinks::synchronous_sink< sinks::syslog_backend >());  
               sink->set_formatter  
		(  
			expr::format("%1%")  
			%expr::smessage  
		);  
		  
		sink->locked_backend()->set_target_address(hostname);  
		  
		//sink->set_filter(expr::has_attr<string>(uniquevalue));  
		// Add the sink to the core  
		logging::core::get()->add_sink(sink);  
		  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2019-04-08 13:13:21 UTC  
> Url: https://github.com/boostorg/log/issues/81#issuecomment-480826014  

`BOOST_LOG_SEV` is just a wrapper macro. You can format the message the way you want, if you to it manually, as in an example [here](https://www.boost.org/doc/libs/1_69_0/libs/log/doc/html/log/tutorial/sources.html). You can also write a stream manipulator for outputting your vector into a stream (an example is given [here](https://stackoverflow.com/questions/32524347/boost-log-stdexception-formatter-unable-to-find-operator-overload-in-own-nam/32538936#32538936)) or use [Boost.Range](https://www.boost.org/doc/libs/1_69_0/libs/range/doc/html/index.html) with `BOOST_LOG_SEV` or any other logging macro.  
  
Note that all that will create a *single* log record with all the contents you put in it. There may be limits imposed by syslog on the size of a single log record.

---

## Comment 2

> Username: mrunmayi29  
> Created at: 2019-04-09 02:35:58 UTC  
> Url: https://github.com/boostorg/log/issues/81#issuecomment-481078515  

Thank you for your response. All these methods will send a single log record. Is there any way I can send multiple log records at the same time? I mean BOOST_LOG_SEV or any other macro sends only 1 record at a time. I have a vector of 1000 records. It will create 1000 network calls. Instead is it possible that I send 1000 (or more than 1) logs at a time to reduce network overload?

---

## Comment 3

> Username: Lastique  
> Created at: 2019-04-09 18:18:14 UTC  
> Url: https://github.com/boostorg/log/issues/81#issuecomment-481371678  

No, you'll have to loop over your vector and log entries one by one. There is no difference in terms of network load since syslog is UDP-based and each record is one UDP packet anyway.
