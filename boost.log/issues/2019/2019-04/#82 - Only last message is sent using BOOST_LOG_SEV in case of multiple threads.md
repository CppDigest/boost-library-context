# #82 - Only last message is sent using BOOST_LOG_SEV in case of multiple threads. [Closed]

> Username: alleidd1945  
> Created at: 2019-04-11 11:00:37 UTC  
> Updated at: 2019-04-11 23:32:07 UTC  
> Closed at: 2019-04-11 23:31:38 UTC  
> Url: https://github.com/boostorg/log/issues/82  

I am using BOOST LOG in my multi-threaded(maximum 3 at a time) application to send logs to remote rsyslog. I am using singleton class to maintain only 1 sink and using different loggers for each thread. Logs are being sent to remote syslog but many times I am facing this issue : If I send 5(more than 1) logs from a thread, 6 (more than 1) logs from another thread ,then   
1) In case of t1: only last log is sent to rsyslog and other logs are missing  
2) In case of t2: all logs are sent.  
Even if I create 1 thread then also all other logs are missing except last log.   
Is this because of singleton class and BOOST_LOG_SEV and syslog sink is not able to handle multiple threads or some other issue? Can someone please help?  
(My code was working when syslog sink was global and all logs were sent to rsyslog properly, when I changed it to singleton object it started above behaviour. )  
  
CODE:  
header file  
```  
class BaseHelper  
{  
      vector<string>sendlogs;  
};  
class SysLogSink  
{  
	static SysLogSink* _instance;  
	boost::shared_ptr< sinks::synchronous_sink< sinks::syslog_backend > > sink;  
public:  
	static SysLogSink* getInstance();  
	SysLogSink();  
	int initialize(string hostname);  
};  
  
class SysLogHelper : public BaseHelper  
{  
	string tag;  
	SysLogSink* sysLogSink;  
	src::severity_logger<> lg;  
public:  
	SysLogHelper();  
	int initialize(string hostname);  
	int sendLogstoSyslog(string message);  
	void setPropertiesMessage(string tag);  
};  
  
```  
  
Cpp file:  
  
```  
BaseHelper::BaseHelper()  
{  
  sendLogs.clear();  
}  
  
void BaseHelper::addMessagesToVectorAndSend()  
{  
//It will do some processing and add messages to sendLogs vector.  
for(each record in vector)  
   sendLogstoSyslog(1 record at a time);  
  
//Here if I send 6(more than 1) messages, I am able to see only last message in rsyslog's /var/log/messages, Is it because of some thread issue or am I doing something wrong here?  
}  
  
SysLogSink * SysLogSink::_instance = NULL;  
  
SysLogSink* SysLogSink::getInstance()  
{  
	if (_instance == NULL)  
	{  
		_instance = new SysLogSink();  
	}  
	return _instance;  
}  
  
SysLogSink::SysLogSink()  
{  
	sink = boost::shared_ptr<sinks::synchronous_sink< sinks::syslog_backend >>(new sinks::synchronous_sink< sinks::syslog_backend >());  
}  
  
int SysLogSink::initialize(string hostname)  
{  
	try  
	{  
		sink->set_formatter  
		(  
			expr::format("%1%")  
			% expr::smessage  
		);  
		sink->locked_backend()->set_target_address(hostname);  
		logging::core::get()->add_sink(sink);  
	}  
	catch (std::exception& e)  
	{  
		return 1;  
	}  
	return 0;  
}  
SysLogHelper::SysLogHelper()  
{  
	tag = "";  
	sysLogSink = NULL;  
}  
  
void SysLogHelper::setPropertiesMessage(string tag)  
{  
	this->tag = tag;  
}  
int SysLogHelper::sendLogstoSyslog(string message)  
{  
	try  
	{  
		message = tag + message;  
		BOOST_LOG_SEV(lg, sinks::syslog::alert) << message;  
	}  
	catch (std::exception& e)  
	{  
		return 1;  
	}  
	return 0;  
}  
  
int SysLogHelper::initialize(string hostname)  
{  
	try  
	{  
		sysLogSink = SysLogSink::getInstance();  
		if (sysLogSink)  
		{  
			sysLogSink->initialize(hostname);  
		}  
	}  
	catch(std::exception& e)  
	{  
		return 1;  
	}  
	return 0;  
}  
  
  
void startprocessing()  
{  
   SysLogHelper sh;  
   sh.initialize("172.16.56.233");  
string tag1 = "some unique tag for each thread";  
sh.setPropertiesMessage(/*add unique tag for each thread*/ tag1);  
   sh.addMessagesToVectorAndSend();  
}  
  
int main()  
{  
  
//creating multiple threads t1, t2,and t3.  
            //each thread calls startprocessing function.  
  
  
}  
  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2019-04-11 23:31:34 UTC  
> Updated at: 2019-04-11 23:32:07 UTC  
> Url: https://github.com/boostorg/log/issues/82#issuecomment-482372206  

First of all, you're missing thread synchronization around `SysLogHelper::initialize`. Second, you're initializing the sink and adding it to the logging core multiple times. The latter may not be the reason for missing log records, but still a bug. Third, syslog servers may eliminate similar log messages to mitigate log spamming. Lastly, the syslog protocol, being based on UDP, is not reliable. I'd suggest you first debug your code with a file sink or console, and switch to syslog when it's working as expected.
