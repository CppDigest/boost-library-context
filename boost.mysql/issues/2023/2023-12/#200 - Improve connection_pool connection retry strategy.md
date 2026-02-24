# #200 - Improve connection_pool connection retry strategy [Open]

> Username: anarthal  
> Created at: 2023-12-20 16:40:52 UTC  
> Updated at: 2023-12-20 16:40:52 UTC  
> Url: https://github.com/boostorg/mysql/issues/200  

* The retry strategy is currently very simple (linear time between retries) and not configurable. When there are a lot of connections, this can saturate the network. A configurable retry strategy may be an option. An exponential backoff may be beneficial.  
* When `initial_size` is big, all connections attempt an establishment try at the same time. This can again cause congestion. We may want to limit this.  
* This may also cause trouble with https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_max_connect_errors
