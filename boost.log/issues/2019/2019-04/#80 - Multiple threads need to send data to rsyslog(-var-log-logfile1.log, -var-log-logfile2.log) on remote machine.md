# #80 - Multiple threads need to send data to rsyslog(/var/log/logfile1.log, /var/log/logfile2.log) on remote machine [Closed]

> Username: mrunmayi29  
> Created at: 2019-04-05 07:25:35 UTC  
> Updated at: 2019-04-06 08:50:01 UTC  
> Closed at: 2019-04-06 08:50:01 UTC  
> Url: https://github.com/boostorg/log/issues/80  

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
> Created at: 2019-04-06 08:50:01 UTC  
> Url: https://github.com/boostorg/log/issues/80#issuecomment-480487254  

Duplicates https://github.com/boostorg/log/issues/79.
