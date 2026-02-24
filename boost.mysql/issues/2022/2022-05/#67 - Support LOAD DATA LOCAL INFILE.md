# #67 - Support LOAD DATA LOCAL INFILE [Open]

> Username: anarthal  
> Created at: 2022-05-12 09:45:09 UTC  
> Updated at: 2022-05-12 09:45:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/67  

https://dev.mysql.com/doc/refman/8.0/en/load-data.html  
  
When issuing such a query, the server answers with a GET_MORE_CLIENT_DATA (type 0xfb) as seen in [this diagram](https://dev.mysql.com/doc/internals/en/com-query-response.html#packet-COM_QUERY_Response). Add support for this.
