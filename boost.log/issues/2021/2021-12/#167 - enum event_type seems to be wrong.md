# #167 - enum event_type seems to be wrong [Closed]

> Username: humayunkhan  
> Created at: 2021-12-01 20:07:23 UTC  
> Updated at: 2021-12-06 13:03:44 UTC  
> Closed at: 2021-12-03 15:10:41 UTC  
> Url: https://github.com/boostorg/log/issues/167  

In the following header file:  
https://github.com/boostorg/log/blob/master/include/boost/log/sinks/event_log_constants.hpp  
  
The enum:  
//! Windows event types  
    enum event_type  
    {  
        success = 0,                 //!< Equivalent to EVENTLOG_SUCCESS  
        info = 4,                    //!< Equivalent to EVENTLOG_INFORMATION_TYPE  
        warning = 2,                 //!< Equivalent to EVENTLOG_WARNING_TYPE  
        error = 1                    //!< Equivalent to EVENTLOG_ERROR_TYPE  
    };  
  
is not correct in my opinion.  
  
According to latest Microsoft documentation, valid values for event_type are 0, 1, 2 and 3  only.  
Please refer to the following link to verify my claim:  
https://docs.microsoft.com/en-in/windows/win32/eventlog/message-text-files#:~:text=message%20definition%20section.-,SeverityNames%3D,-(name%3Dnumber%5B%3Aname  
  
The correct enum should be like this:  
//! Windows event types  
    enum event_type  
    {  
        success = 0,                 //!< Equivalent to EVENTLOG_SUCCESS  
        info = 1,                    //!< Equivalent to EVENTLOG_INFORMATION_TYPE  
        warning = 2,                 //!< Equivalent to EVENTLOG_WARNING_TYPE  
        error = 3                    //!< Equivalent to EVENTLOG_ERROR_TYPE  
    };

---

## Comment 1

> Username: Lastique  
> Created at: 2021-12-03 15:10:41 UTC  
> Updated at: 2021-12-03 15:20:59 UTC  
> Url: https://github.com/boostorg/log/issues/167#issuecomment-985597471  

`event_type` defines constants that are mentioned in the comments. These values are passed to [`ReportEvent`](https://docs.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-reporteventa) as `wType`. The values match `EVENTLOG_*` constants in Windows SDK, so they are correct.  
  
The page you link to describes severity levels, which is not the same as event type. Severity levels are described in the [message file](https://github.com/boostorg/log/blob/8024d7c9c9b83268a101f2c6aae9a95b2ba765a4/src/windows/simple_event_log.mc), and there Boost.Log does use values from 0 to 3. Severity levels are further associated with message templates, each template having an id. That id is supplied to `ReportEvent` (as `dwEventID`), along with substitution strings and event type, to register in the event log.

---

## Comment 2

> Username: humayunkhan  
> Created at: 2021-12-06 13:03:44 UTC  
> Url: https://github.com/boostorg/log/issues/167#issuecomment-986755127  

Hi.. Thanks for the clarification.  
  
I need some help on https://github.com/boostorg/log/issues/169  
Could you please look into it?
