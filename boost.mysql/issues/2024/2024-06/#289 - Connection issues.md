# #289 - Connection issues [Closed]

> Username: jphz  
> Created at: 2024-06-24 12:55:46 UTC  
> Updated at: 2025-01-01 12:14:37 UTC  
> Closed at: 2024-06-24 15:08:47 UTC  
> Url: https://github.com/boostorg/mysql/issues/289  

I initialized the default minimum of 1000 connections, but after a while, the connections were released. When my SQL request arrived, I reconnected. However, on the Windows platform, I encountered a large number of 10053 errors, which caused the SQL request to be unable to be executed. What should I do?

---

## Comment 1

> Username: jphz  
> Created at: 2024-06-24 12:57:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186520723  

```cpp  
void DBQuery::start(std::shared_ptr<boost::mysql::connection_pool> pool) {  
    pool->async_get_connection(  
        std::chrono::seconds(0),  
        [this, self = shared_from_this()](boost::system::error_code ec, boost::mysql::pooled_connection connection) {  
            if (ec) {  
                onFail(ec, "connect");  
                return;  
            }  
  
            _conn = std::move(connection);  
            _conn->async_execute(_sql, _results, _errorInfo,  
                                 [this, self = shared_from_this()](boost::system::error_code ec) {  
                                     if (ec) {  
                                         onFail(ec, "query");  
                                         return;  
                                     }  
  
                                     LOG_DEBUG(gLogger, "DBService::query type:{} sn:{} sql:{}", _type, _sn, _sql);  
                                     _callback(true, self);  
                                 });  
        });  
}  
  
void DBQuery::onFail(boost::system::error_code ec, std::string_view prefix) {  
    LOG_ERROR(gLogger, "DBService::{} type:{} sn:{} sql:{} code:{} error:{}", prefix, _type, _sn, _sql, ec.value(),  
              ec.message());  
    _callback(false, shared_from_this());  
}  
```  
24-06-24 20:50:25 29400 error DBService::query type:2 sn:1125899906850706 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁84@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 6916 error DBService::query type:2 sn:1125899906850708 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁86@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 29400 error DBService::query type:2 sn:1125899906850713 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁87@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 32532 error DBService::query type:2 sn:1125899906850710 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁88@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 2612 error DBService::query type:2 sn:1125899906850699 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁70@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 29400 error DBService::query type:2 sn:1125899906850709 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁89@abc123'; code:5 error:Mismatched sequence numbers  
24-06-24 20:50:25 6916 error DBService::query type:2 sn:1125899906850711 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁90@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 2612 error DBService::query type:2 sn:1125899906850716 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁91@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 12928 error DBService::query type:2 sn:1125899906850714 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁92@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 12668 error DBService::query type:2 sn:1125899906850719 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁93@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 29400 error DBService::query type:2 sn:1125899906850718 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁94@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 32532 error DBService::query type:2 sn:1125899906850717 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁95@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 12928 error DBService::query type:2 sn:1125899906850727 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁97@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 29400 error DBService::query type:2 sn:1125899906850720 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁96@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 29400 error DBService::query type:2 sn:1125899906850721 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁99@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 29400 error DBService::query type:2 sn:1125899906850722 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁98@abc123'; code:5 error:Mismatched sequence numbers  
24-06-24 20:50:25 32532 error DBService::query type:2 sn:1125899906850723 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁101@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 6916 error DBService::query type:2 sn:1125899906850724 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁100@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 6916 error DBService::query type:2 sn:1125899906850726 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁102@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 6916 error DBService::query type:2 sn:1125899906850729 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁105@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 12928 error DBService::query type:2 sn:1125899906850728 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁104@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 29400 error DBService::query type:2 sn:1125899906850733 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁106@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。  
24-06-24 20:50:25 29400 error DBService::query type:2 sn:1125899906850730 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁108@abc123'; code:5 error:Mismatched sequence numbers  
24-06-24 20:50:25 5708 error DBService::query type:2 sn:1125899906850731 sql:SELECT `accountID` FROM `account` WHERE `passport`='寮犱笁107@abc123'; code:10053 error:你的主机中的软件中止了一个已建立的连接。

---

## Comment 2

> Username: anarthal  
> Created at: 2024-06-24 13:02:08 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186532558  

What's your server's `max_connections`? A possible cause is increasing the number of connections in `pool_params` but not increasing it in the server. What's the result of running `SELECT @@max_connections` in your MySQL server?

---

## Comment 3

> Username: jphz  
> Created at: 2024-06-24 13:03:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186535594  

![image](https://github.com/boostorg/mysql/assets/30751794/bf1e3cf2-7f40-405b-af00-8250642c8ff1)

---

## Comment 4

> Username: jphz  
> Created at: 2024-06-24 13:04:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186538089  

I use SHOW STATUS LIKE 'Threads connected'; At the beginning, it was 1000, but after a while, it will become 1

---

## Comment 5

> Username: anarthal  
> Created at: 2024-06-24 13:07:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186542503  

After how much time did the connections die?  
Reading [the documentation for that error code](https://learn.microsoft.com/en-us/windows/win32/winsock/windows-sockets-error-codes-2) it might be related to anti-malware software killing connections. Is your server running under your same Windows machine too?

---

## Comment 6

> Username: jphz  
> Created at: 2024-06-24 13:14:24 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186557418  

They are all on the same machine and disappear in about a minute

---

## Comment 7

> Username: jphz  
> Created at: 2024-06-24 13:21:47 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186572275  

I think I found the problem. The wait_time setting is too short and only lasts for 100 seconds.

---

## Comment 8

> Username: jphz  
> Created at: 2024-06-24 13:25:30 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186579896  

If it is for this reason, why do disconnected connections still exist in the connection pool? Or why did I receive a disconnected connection?

---

## Comment 9

> Username: anarthal  
> Created at: 2024-06-24 14:03:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186662601  

Do you mean [`wait_timeout`](https://dev.mysql.com/doc/refman/8.4/en/server-system-variables.html#sysvar_wait_timeout)? Having it set to 100 is a problem, yes.  
  
Connections in the pool don't have a way of detecting that the connection was closed by the server, at least not immediately. Unused connections actively check for liveliness by using `async_ping`, every `pool_params::ping_interval` (by default, 1h). Pings prevent connections from closing, and trigger reconnections if they fail. That 1h is less than the default `wait_timeout`.  
  
In your case, the server closes the connection but the pool doesn't have a way to detect it. When you get a connection, your operation fails and the connection gets returned to the pool. The pool tries to reset the connection and fails (it's closed), and then reconnects it.  
  
If you need to set `wait_timeout` to a value smaller than 1h, you will need to adjust `ping_interval`. It should always be less than `wait_timeout`.

---

## Comment 10

> Username: jphz  
> Created at: 2024-06-24 14:06:13 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186667940  

If it defaults to 8 hours, there won't be any problem, right.

---

## Comment 11

> Username: jphz  
> Created at: 2024-06-24 14:09:46 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186675604  

/**  
     * \brief The health-check interval.  
     * \details  
     * If a connection becomes idle and hasn't been handed to the user for  
     * `ping_interval`, a health-check will be performed (using \ref any_connection::async_ping).  
     * Pings will be sent with a periodicity of `ping_interval` until the connection  
     * is handed to the user, or a ping fails.  
     * \n  
     * Set this interval to zero to disable pings.  
     * \n  
     * This value must not be negative.  
     */  
I saw the code comments, and the unused connection will ping once every hour. Due to my previous setting of only 100sec, the connection was closed and not detected. Then, I used this connection again, resulting in network reconnection issues.

---

## Comment 12

> Username: anarthal  
> Created at: 2024-06-24 15:08:47 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186806075  

Great. I've opened an issue to state this more clearly in the docs. I'll be closing this one.

---

## Comment 13

> Username: jphz  
> Created at: 2024-06-24 15:44:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2186879761  

Thank you very much.

---

## Comment 14

> Username: bruno-viva  
> Created at: 2025-01-01 00:51:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2566779618  

> Great. I've opened an issue to state this more clearly in the docs. I'll be closing this one.  
  
Hi! I am a bit confused: shouldn't ping_interval be lower than the database wait_timeout? From your previous comment:  
  
> If you need to set wait_timeout to a value smaller than 1h, you will need to adjust ping_interval. It should always be less than wait_timeout.  
  
That means `ping_interval < wait_timeout`, right? The code doc says the opposite: `This value must not be negative. It should be bigger than the server's idle timeout`  
  
Thanks in advance

---

## Comment 15

> Username: anarthal  
> Created at: 2025-01-01 12:14:07 UTC  
> Updated at: 2025-01-01 12:14:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/289#issuecomment-2566983579  

Yes, you're definitely right. `ping_interval < wait_timeout` is the correct one. It looks like I was thinking in frequencies when I wrote that. Thanks for reporting.
