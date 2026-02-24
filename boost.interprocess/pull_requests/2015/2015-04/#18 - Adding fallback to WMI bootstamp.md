# #18 Adding fallback to WMI bootstamp [Closed]

> Username: teeks99  
> Created at: 2015-04-16 13:52:05 UTC  
> Updated at: 2021-02-11 12:59:05 UTC  
> Closed at: 2021-02-11 12:59:04 UTC  
> Url: https://github.com/boostorg/interprocess/pull/18  

If the event log has been cleared, it will no longer contain the startup timestamp. In this case, we could fallback to using the WMI derived timestamp instead of the event log timestamp.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-02-11 12:59:04 UTC  
> Url: https://github.com/boostorg/interprocess/pull/18#issuecomment-777437814  

Reviewing old bugs. The fallback wouldn't solve the problema as one early launched process would obtain the EventLog bootstamp, whereas a process launched much later would take the WMI bootstamp. They could not communicate. Current Interprocess uses registry values from the Session Manager so there is no problem when the log has been cleared.

---
