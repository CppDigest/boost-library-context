# #52 - Boos.Mysql does not work with latest boost version [Closed]

> Username: nesc1  
> Created at: 2021-10-10 07:33:31 UTC  
> Updated at: 2022-09-12 21:28:59 UTC  
> Closed at: 2022-03-22 18:35:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/52  

Hi, I was trying the library with the latest boost version and it seems there is some problem with it.  
  
Trying with boost version 1.76 everything works as expected, but with boost version 1.77 connection action always return:  
  
Bad file descriptor (9)  
  
on the asio connection.  
  
That code is a bit hard to understand, so if you could help understanding what is the problem will be great.  
Best regards,  
Nuno

---

## Comment 1

> Username: anarthal  
> Created at: 2021-10-11 19:58:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-940400272  

Hi @nesc1,  
  
Thanks for opening the issue. I have not been having time to work on this library for these past months, so I haven't tested this yet with Boost 1.77 - it is possible that we need some change to make it work.  
  
Are you trying any of the examples? Otherwise, could you please post some code snippet, just in case I can help?  
  
Please also bear in mind that this library is still unreleased, and it may incorporate breaking changes in the future (it has not yet been accepted as part of Boost).  
  
Sorry for the inconvenience,  
Ruben.

---

## Comment 2

> Username: nesc1  
> Created at: 2021-10-13 15:15:32 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-942408456  

Hi first of all great work, and second is just fine until 1.76 of boost version.  
  
Any example should prove what i'm saying.. it fails in connection... but i did not try your samples... only mine, but i don't do anything different.. but i can test it and then send you some feedback about it..  
  
Best regards,  
Nuno

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-10-13 15:29:47 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-942422077  

I'm happy to take a look. @nesc1 are you able to post a minimal  
variable program plus to demonstrate the issue? So that I can replicate  
locally easily?  
  
  
  
On Wed, 13 Oct 2021 at 17:15, nesc1 ***@***.***> wrote:  
  
> Hi first of all great work, and second is just fine until 1.76 of boost  
> version.  
>  
> Any example should prove what i'm saying.. it fails in connection... but i  
> did not try your samples... only mine, but i don't do anything different..  
> but i can test it and then send you some feedback about it..  
>  
> Best regards,  
> Nuno  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/anarthal/mysql/issues/52#issuecomment-942408456>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSINAUD5PZ67ZNAOFJLUGWPB7ANCNFSM5FWFJ3KA>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
>

---

## Comment 4

> Username: nesc1  
> Created at: 2021-10-14 08:41:22 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-943142979  

Hi sorry @madmongo1 , but I haven't yet loose some more time with this, only on the next weekend i should be able to help, but try to compile with boost v1.77 and do a simple connect on a mysql database... that should trigger the problem.

---

## Comment 5

> Username: anarthal  
> Created at: 2022-03-22 18:35:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1075489150  

Fixed by 86e0eacd6ad3f47a201c75067f3327ac1e12ee36.

---

## Comment 6

> Username: madmongo1  
> Created at: 2022-07-17 18:25:50 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1186585133  

This is an error at the network layer.  
Your server is either not listening or firewalled.

---

## Comment 7

> Username: anarthal  
> Created at: 2022-07-17 18:31:22 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1186585907  

As @madmongo1 pointed out, this means that the physical TCP connection could not be established. If you are running the example as is (i.e. `boost_mysql_example example_user example_password`), your server is assumed to be listening on localhost.  
  
When typing `mysql --version`, `mysql` is likely using a UNIX socket connection instead of TCP. Could you please post the result of running the following command?  
  
```  
mysql -u root --host localhost --port 3306 --protocol tcp  
```

---

## Comment 8

> Username: nesc1  
> Created at: 2022-07-17 18:47:04 UTC  
> Updated at: 2022-07-17 18:51:50 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1186588066  

yes indeed sorry for that i didn't notice that...but unfortanelly i'm not bein able to use the new version on a normal mysql server  
  
i tested with your examples and it always give me this:  
  
```  
❯ ./example/boost_mysql_example_query_sync <user> <pwd> <host>  
Error: The server does not support the minimum required capabilities to establish the connection, error code: mysql:65539  
```  
  
and in my original code it makes the connection but any select sql fails with exception...  
  
  
doing your command it returns the following:  
  
```  
❯ mysql -u <username> -p --host localhost --port 3306 --protocol tcp  
Enter password:  
Welcome to the MariaDB monitor.  Commands end with ; or \g.  
Your MariaDB connection id is 59  
Server version: 10.3.34-MariaDB-0ubuntu0.20.04.1 Ubuntu 20.04  
  
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.  
  
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.  
  
MariaDB [(none)]>  
  
```

---

## Comment 9

> Username: anarthal  
> Created at: 2022-07-17 22:00:26 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1186614617  

This is very likely caused by TLS being disabled or misconfigured on the server. `tcp_ssl_connection` by default requires that the server supports TLS. You can force the same behavior in `mysql`:  
  
```  
mysql -u root --host localhost --port 3306 --protocol tcp --ssl-mode required  
```  
  
If TLS is not properly configured, the above will fail. You can either configure TLS properly by using MySQL config files (https://dev.mysql.com/doc/refman/8.0/en/using-encrypted-connections.html) or use plaintext connections by using `tcp_connection` instead.

---

## Comment 10

> Username: nesc1  
> Created at: 2022-07-17 23:46:00 UTC  
> Updated at: 2022-07-17 23:59:28 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1186632578  

I @anarthal thank you for your help, indeed using tcp_connection is the key to make a successful connection.  
  
Trying your command it does not know the option ssl-mode:  
  
```  
❯ mysql -u <user> -p --host localhost --port 3306 --protocol tcp --ssl-mode required  
mysql: unknown option '--ssl-mode'  
```  
but when i do some selects I'm getting this kind of errors:  
  
```  
[E][43:58] DB error: An unexpected value was found in a server-received message, error code: An unexpected value was found in a server-received message (65540)  
select telegramid,balance from userbalance  
[E][43:58] DB error: Mismatched sequence numbers, error code: Mismatched sequence numbers (65538)  
select userid, otherid from automaticeventassignment  
[E][43:58] DB error: Mismatched sequence numbers, error code: Mismatched sequence numbers (65538)  
```  
  
Do you know what is happening?  
  
the table is simple enought:  
  
```  
CREATE TABLE userbalance  
(  
    id BIGINT NOT NULL,  
    balance FLOAT NOT NULL,  
    createdtime DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,  
    lastupdate DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP  
                ON UPDATE CURRENT_TIMESTAMP,  
    primary key (id)  
);  
```  
  
and select also:  
`select id,balance from userbalance`  
  
code:  
  
```  
auto result = conn.query(sql);  
auto dbrows = result.read_all(); <<< exception happens here  
for (const auto& row : dbrows)  
{  
    convert(row);  
}  
```

---

## Comment 11

> Username: anarthal  
> Created at: 2022-07-18 06:51:49 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1186830190  

Could you please share a gist with the entire code you're using, please?

---

## Comment 12

> Username: nesc1  
> Created at: 2022-07-18 08:45:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1186930440  

can you explain what is a gist ... sorry don't know how to do it...

---

## Comment 13

> Username: anarthal  
> Created at: 2022-07-18 09:36:01 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1186981009  

A way to share code: https://gist.github.com/  
Google is your friend.

---

## Comment 14

> Username: nesc1  
> Created at: 2022-07-18 14:10:03 UTC  
> Updated at: 2022-07-18 14:34:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1187531809  

(thank you..yep indeed i could do a google search before i asked :P)  
  
Humm ok that's strange, where the code throws the exception, only doing that select does not cause the problem (https://gist.github.com/nesc1/93231380f10b7d8a9cad2fbdc8adcfba), only with connect and doing the problematic select...  It must something before... but with previous version it does not happen, but with this new one it happens... I only changed the source code of the boost::mysql and change from fetch_xxx calls to read_xxx calls...   
  
edit: I added the previous call to the gist but even with that there is no problem...so it must be some call to the database that ruins some state...I'm saying...  
  
edit2: indeed this code crashes, because of the 1º call that uses the prepare statement that seems to work fine, but causes the misbehaviour on the second call (select), but the problem is that not all data read from preparestatement causes the problem only one it seems... trying to understand why...

---

## Comment 15

> Username: anarthal  
> Created at: 2022-07-18 14:40:26 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1187585069  

From the errors you're getting, it seems your problem is related to not fully reading resultsets before engaging into further operations.  
  
When you call `connection::query` or `prepared_statement::execute` you *must* read the resultset you get *completely* (a successful call to `resultset::read_all` does the trick) before engaging into any subsequent operation, including further queries, closing statements or preparing other statements. From your description, your code is failing to do that under some circumstance.

---

## Comment 16

> Username: nesc1  
> Created at: 2022-07-18 14:46:40 UTC  
> Updated at: 2022-07-18 14:56:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1187593085  

But in my code I always use read_all(..) or something like this:  
  
```  
boost::mysql::row row;  
while (result.read_one(row))  
{  
//i do not break these operations  
}  
```  
  
resultset< Stream > destructor should not clean all the pending operations? or do i need to call something special to close it?  
  
edit: by the way the error code that i'm getting is: 65540...  
  
and why the same code with previous version (before the read_xxx change) worked and not now...

---

## Comment 17

> Username: nesc1  
> Created at: 2022-07-18 17:56:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1187953025  

never mind... I already loose too much time with this... what I did to fix the problem was simple drop the table and create it again in mysql and it worked after this... don't really know what was the problem...  
  
@anarthal thank you anyway for your help.

---

## Comment 18

> Username: nesc1  
> Created at: 2022-08-25 10:58:31 UTC  
> Updated at: 2022-08-25 11:15:20 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1227104720  

this still happen... even if i drop the table and recreate it again... the boost::mysql does not read the column float or double but decimal reads... returning:  
`An unexpected value was found in a server-received message (65540)`  
still don't understand why...  
  
table is simple:  
```sql  
CREATE TABLE IF NOT EXISTS `channellevels` (  
  `channelid` bigint(20) NOT NULL,  
  `levelid` mediumint(8) unsigned NOT NULL,  
  `bonusrank` smallint(6) NOT NULL,  
  `extrawin` float NOT NULL,  
  `penalty` float NOT NULL,  
  PRIMARY KEY (`channelid`,`levelid`)  
) ENGINE=InnoDB DEFAULT CHARSET=latin1;  
```  
  
and the select simple:  
`select levelid, channelid, bonusrank, extrawin, penalty from channellevels order by levelid`  
  
(note: it must have values)  
  
the problem only happen on `query(sql).read_all())` not on:  
```  
stm = m_conn->prepare_statement(sql);  
result      = stm.execute(params);  
auto dbrows = result.read_all();  
```

---

## Comment 19

> Username: Jackson-soft  
> Created at: 2022-09-06 10:22:43 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1237959652  

boost 1.79 have the same error

---

## Comment 20

> Username: anarthal  
> Created at: 2022-09-12 21:28:14 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1244508939  

I'm still unable to reproduce the issue. I'm running [this code](https://gist.github.com/anarthal/2a91848debecf0681ef86718144df0a3) under Boost 1.80, clang 12, Linux and MySQL 8.0.30.  
  
Could you please provide a minimum reproducible example in a public gist where the issue occurs? Otherwise I have no way to look deeper into this issue.

---

## Comment 21

> Username: anarthal  
> Created at: 2022-09-12 21:28:59 UTC  
> Url: https://github.com/boostorg/mysql/issues/52#issuecomment-1244510260  

@Jackson-soft are you experimenting the same issue as @nesc1?
