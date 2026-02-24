# #157 - Logs does not come with new updated hostname [Closed]

> Username: fuqianz  
> Created at: 2021-07-27 22:05:33 UTC  
> Updated at: 2021-07-29 09:03:35 UTC  
> Closed at: 2021-07-29 09:03:34 UTC  
> Url: https://github.com/boostorg/log/issues/157  

Is there a way to get the new updated hostname when sending logs?

---

## Comment 1

> Username: Lastique  
> Created at: 2021-07-28 08:32:30 UTC  
> Url: https://github.com/boostorg/log/issues/157#issuecomment-888121930  

I don't understand the question, please elaborate. What's your use case, what you mean by "updated hostname" and "sending logs"?

---

## Comment 2

> Username: fuqianz  
> Created at: 2021-07-29 00:06:22 UTC  
> Url: https://github.com/boostorg/log/issues/157#issuecomment-888699547  

We have services integrated boost logging with BOOST_LOG_GLOBAL_LOGGER_INIT. Services are running in CentOS. When we updated OS hostname, services are still using the old hostname to sending logs to local syslog daemon. Let me know if this answered your questions. Thanks for the reply.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-07-29 09:03:34 UTC  
> Url: https://github.com/boostorg/log/issues/157#issuecomment-888941148  

If you're using `syslog_backend` with `use_impl = native` (which is the default on Linux) then Boost.Log uses the standard [syslog](https://linux.die.net/man/3/syslog) API which does not allow to specify the hostname. The API interacts with the local syslog service, which writes the log messages to a file or sends to a remote host. That service also uses the local host name. You should see if you can make that service to update its cached local host name, possibly by sending it `SIGHUP`.  
  
If you're using `use_impl = udp_socket_based` then the syslog backend generates syslog messages itself and sends them to a remote host. In this case, it uses the local host name obtained when the first `syslog_backend` was created and there is no way to update it - you will have to restart the process to reinitialize the local host name. This is done deliberately, to optimize log record generation.
