# #188 - syslog unable to filter based on priority. [Closed]

> Username: cgskumar77  
> Created at: 2022-07-05 17:44:16 UTC  
> Updated at: 2022-07-06 00:16:08 UTC  
> Closed at: 2022-07-06 00:16:08 UTC  
> Url: https://github.com/boostorg/log/issues/188  

Hi,  
  
My rsyslog is unable to apply selectors to the priority portion of the logs sent by boost.log.  
  
I'm using boost_1_79 with native syslog on Ubuntu 18.04.  
  
I'm following instructions from https://www.boost.org/doc/libs/1_79_0/libs/log/doc/html/log/detailed/sink_backends.html#log.detailed.sink_backends.syslog  
  
Here is the code snippet:  
````  
// Create a backend  
boost::shared_ptr< sinks::syslog_backend > backend(new sinks::syslog_backend(  
        keywords::facility = sinks::syslog::user,               1  
        keywords::use_impl = sinks::syslog::native              2  
));  
  
// Set the straightforward level translator for the "Severity" attribute of type int  
backend->set_severity_mapper(sinks::syslog::direct_severity_mapping< int >("Severity"));  
  
// . . .  
BOOST_LOG_SEV(lg, sinks::syslog::alert) << "An alert message.";  
````  
  
Here is my /etc/rsyslog.d/50-default.conf:  
`user.alert           -/var/log/user.log`  
  
This doesn't write a record in user.log file unless I change the selector to `user.*`. I have also tried to explicitly define a custom mapping as well with the same results.  
  
However, I don't have any such problems if I use the `logger `utility from the command line to write logs. This suggests that the logs from boost.log doesn't have the right severity in them. Is that possible? Or I have something wrong in my code although it is just cut and paste from the tutorial mentioned above.  
  
Any ideas on how to resolve this?  
  
Thanks,  
Senthil.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-07-05 21:13:37 UTC  
> Url: https://github.com/boostorg/log/issues/188#issuecomment-1175504137  

You should check the type of the severity level attribute in your logger `lg`. You're specifying `int` in the severity mapping function (`direct_severity_mapping< int >`), so if the severity attribute value has a different type the mapping function won't be able to extract it from the log record and will return the default value, which is `info`.

---

## Comment 2

> Username: cgskumar77  
> Created at: 2022-07-05 23:12:14 UTC  
> Url: https://github.com/boostorg/log/issues/188#issuecomment-1175590423  

Thank you very much, Andrey. The following change helped:  
```  
-    src::logger lg;  
+    src::severity_logger<int> lg { keywords::severity = sinks::syslog::error };  
```  
I should have read https://www.boost.org/doc/libs/1_79_0/libs/log/doc/html/log/detailed/sources.html#log.detailed.sources.severity_level_logger  
  
Thanks,  
Senthil.
