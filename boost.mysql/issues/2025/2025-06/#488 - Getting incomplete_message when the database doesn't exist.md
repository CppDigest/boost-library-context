# #488 - Getting incomplete_message when the database doesn't exist [Closed]

> Username: bruno-viva  
> Created at: 2025-06-25 23:51:10 UTC  
> Updated at: 2025-06-26 15:42:34 UTC  
> Closed at: 2025-06-26 15:42:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/488  

Hi!  
  
I am not sure if this is expected behavior, but, when I do:  
  
```cpp  
    boost::asio::io_context context;  
    boost::mysql::any_connection connection(context);  
    boost::mysql::connect_params params;  
    params.server_address.emplace_host_and_port("127.0.0.1", 3306);  
    params.username = "root";  
    params.password = "root";  
    params.database = "test";  
    params.multi_queries = true;  
  
    boost::mysql::diagnostics diagnostics;  
    boost::mysql::error_code error;  
  
    connection.connect(params, error, diagnostics);  
    if (error) {  
      LOG(ERROR, "Error connecting: {}. Diagnostics: {}/{}", error.what(),  
          diagnostics.client_message(), diagnostics.server_message());  
    }  
```  
  
If the database "test" doesn't exist, I get a `incomplete_message` error:   
  
> An incomplete message was received from the server [mysql.client:1]. Diagnostics: []/[]  
  
I am using MySql version 8.0.35, with boost version 1.87.0.  
  
Let me know if you need more info! Thank you

---

## Comment 1

> Username: anarthal  
> Created at: 2025-06-26 09:34:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/488#issuecomment-3007839724  

Hi @bruno-viva,  
  
If I'm not mistaken, this is a manifestation of https://github.com/boostorg/mysql/issues/468. The fix will be released in Boost 1.89.  
  
Could you please confirm that you are using the `caching_sha2_password` plugin? You can test this by setting `params.ssl = boost::mysql::ssl_mode::disable`, and checking that the connection attempt fails with `client_errc::auth_plugin_requires_ssl`.  
  
Thanks for reporting in any case.  
Ruben.

---

## Comment 2

> Username: bruno-viva  
> Created at: 2025-06-26 14:48:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/488#issuecomment-3008763817  

> boost::mysql::ssl_mode::disable  
  
Thanks @anarthal ! It still fails with `An incomplete message was received from the server` when I disable SSL. When changing to a database that exists, and disabling the SSL, it still works (because my local MySql instance doesn't force SSL).

---

## Comment 3

> Username: anarthal  
> Created at: 2025-06-26 15:05:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/488#issuecomment-3008820589  

My bad, I forgot a step. Could you please run a FLUSH PRIVILEGES immediately before running what I suggested?  
  
I'm trying to verify that your user uses the caching_sha2_password authentication plugin, which is the default in MySQL 8. If you are, the bug is the one I pointed out.

---

## Comment 4

> Username: bruno-viva  
> Created at: 2025-06-26 15:42:34 UTC  
> Url: https://github.com/boostorg/mysql/issues/488#issuecomment-3008935806  

It works now! The error was: `er_bad_db_error`. Closing this bug since #468 should fix it. Thank you!
