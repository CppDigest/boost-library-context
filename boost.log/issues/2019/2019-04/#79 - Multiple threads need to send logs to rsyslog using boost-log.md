# #79 - Multiple threads need to send logs to rsyslog using boost-log [Closed]

> Username: mrunmayi29  
> Created at: 2019-04-05 07:23:55 UTC  
> Updated at: 2019-04-11 23:10:09 UTC  
> Closed at: 2019-04-05 08:04:45 UTC  
> Url: https://github.com/boostorg/log/issues/79  

My application needs to create 1 `syslog` sink for each thread and each thread wants to send data independently.   
  
```  
class AlertsForSyslog  
{  
    string hostname;  
	string message;  
	src::severity_logger<> lg;  
	boost::shared_ptr< sinks::synchronous_sink< sinks::syslog_backend > > sink;  
    public :  
    public :  
	AlertsForSyslog(string hostname);  
int sendMessageToSyslog(string message);  
	void dd();  
};  
  
AlertsForSyslog::AlertsForSyslog(string hostname)  
{  
	this->hostname = hostname;  
	sink = boost::make_shared<sinks::synchronous_sink< sinks::syslog_backend >>();  
}  
  
int AlertsForSyslog::sendMessageToSyslog(string message)  
{  
		this->message = message;  
		sink->set_formatter  
		(  
			expr::format("%1%")  
			%expr::smessage  
		);  
  
		sink->locked_backend()->set_target_address(hostname);  
  
		  
		// Add the sink to the core  
		logging::core::get()->add_sink(sink);  
		  
	return 0;  
}  
  
	void AlertsForSyslog::dd()  
	{  
		BOOST_LOG_SEV(lg, sinks::syslog::alert) << message ;  
	}  
  
int main()  
{  
	cout << "hello";  
	AlertsForSyslog n1 = AlertsForSyslog("172.16.72.239");  
	n1.sendMessageToSyslog("from n1");  
	AlertsForSyslog n2 = AlertsForSyslog("172.16.72.239");  
	n2.sendMessageToSyslog("from n2");  
	AlertsForSyslog n3 = AlertsForSyslog("172.16.72.239");  
	n3.sendMessageToSyslog("from n3");  
	n1.dd();  
	n2.dd();  
	n3.dd();  
	return 0;  
}  
```  
  
Above code gives me this output :  
```  
Apr  4 21:33:58 mypc from n1  
Apr  4 21:33:58 mypc from n1  
Apr  4 21:33:58 mypc from n1  
Apr  4 21:33:58 mypc from n2  
Apr  4 21:33:58 mypc from n2  
Apr  4 21:33:58 mypc from n3  
Apr  4 21:33:58 mypc from n3  
Apr  4 21:33:58 mypc from n2  
Apr  4 21:33:58 mypc from n3  
  
```  
But ideally it should be this right?  
```  
Apr  4 21:33:58 mypc from n1  
Apr  4 21:33:58 mypc from n2  
Apr  4 21:33:58 mypc from n3  
```  
  
How should I improve my code? I want to send logs using different threads on a remote syslog machine.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-04-05 08:04:45 UTC  
> Url: https://github.com/boostorg/log/issues/79#issuecomment-480185798  

In Boost.Log, sinks cannot be assigned to a logging thread or a logger. Every log record you make, from whatever thread, will reach all registered sinks. This explains why you have 3 syslog records per each of your log record. Unless you want different formatting for each of your syslog sinks, there is no reason to have more than one syslog sink.  
  
If you still want multiple sinks, you have to use attributes and filters to ensure that only one sink at a time processes a log record. You can use attributes to associate loggers with sinks (see [here](https://stackoverflow.com/questions/34362871/use-channel-hiearchy-of-boost-log-for-severity-and-sink-filtering/34372526#34372526) for an example with channels) and then use a logger per thread. Or you can tag each thread with an attribute (by adding a [thread-specific](https://www.boost.org/doc/libs/1_69_0/libs/log/doc/html/log/detailed.html#log.detailed.core.core.attribute_sets) attribute with a [constant](https://www.boost.org/doc/libs/1_69_0/libs/log/doc/html/log/detailed/attributes.html#log.detailed.attributes.constant)) and then use [filters](https://www.boost.org/doc/libs/1_69_0/libs/log/doc/html/log/tutorial/advanced_filtering.html) to select records made in particular threads for each sink.

---

## Comment 2

> Username: mrunmayi29  
> Created at: 2019-04-05 11:13:19 UTC  
> Updated at: 2019-04-05 11:14:14 UTC  
> Url: https://github.com/boostorg/log/issues/79#issuecomment-480238279  

Thank you for your response.  
If I created a **global syslog sink** and then used `BOOST_LOG_SEV(lg, sinks::syslog::alert) << message ; ` in every thread to send the message, then will it work? Can you please tell if `BOOST_LOG_SEV` can handle all calls from all the threads or using global syslog sink is not a correct way?

---

## Comment 3

> Username: Lastique  
> Created at: 2019-04-06 08:49:27 UTC  
> Url: https://github.com/boostorg/log/issues/79#issuecomment-480487217  

Yes, you can use a single syslog sink for all your threads. Just use `synchronous_sink` or `asynchronous_sink` sink frontend to make it thread-safe.  
  
If you also want to use a single logger, it will have to be a thread-safe logger (with `_mt` suffix in the name). Although I would recommend multiple loggers to reduce thread contention.

---

## Comment 4

> Username: mrunmayi29  
> Created at: 2019-04-11 11:26:39 UTC  
> Updated at: 2019-04-11 11:27:05 UTC  
> Url: https://github.com/boostorg/log/issues/79#issuecomment-482076107  

In above code : In issue 79: In function : sendMessageToSyslog :  is there any way to check if connection is established with remote syslog server? When we initialize syslog sink, we use set_target_address, add_sink. These methods return type is void. Is there any way I can check if connection is established or not in case of remote machine is down or network problem.  If sink is not established then we can immediately return and don't allow application to send further messages and retry to establish connection. In BOOST_LOG_SEV also we cannot determine if packet is sent or not right because it is udp based. Or is there any way to handle error in both the cases?

---

## Comment 5

> Username: Lastique  
> Created at: 2019-04-11 23:10:08 UTC  
> Url: https://github.com/boostorg/log/issues/79#issuecomment-482360332  

There are no connections in syslog. Either POSIX syslog API is used (which is pretty much printf-like) or UDP is used to send the message one way (syslog has no connections or receipt confirmations on top of UDP). There is no way to know if the message is received in either case.
