# #105 - When is the release schedule for the next version? [Closed]

> Username: chaseYLC  
> Created at: 2023-01-06 03:23:51 UTC  
> Updated at: 2023-01-06 12:37:04 UTC  
> Closed at: 2023-01-06 12:37:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/105  

Recently, there was a need to store binary-type data, so I looked at the documentation, and it seems that the issue related to this has been resolved relatively recently. (https://github.com/boostorg/mysql/issues/104)  
  
I'm currently using version 0.2.0, can I know if there are plans to release the next version?

---

## Comment 1

> Username: anarthal  
> Created at: 2023-01-06 12:37:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/105#issuecomment-1373571424  

You can write binary data in 0.2.0. You treat binary data as they were strings, using `boost::string_view` with `value`. If you use that data in a context where MySQL expects a binary value (e.g. `INSERT INTO mytable (binary_field)...`), MySQL will understand the data as binary without problems. IIRC this is the approach the official MySQL Python driver uses.  
  
For example:  
  
```  
auto stmt = conn.prepare_statement("INSERT INTO mytable (binary_field) VALUES (?)");  
std::vector<std::uint8_t> binary_data = { /* get this from wherever you need. You may use collections other than vector */ };  
boost::string_view binary_view (reinterpret_cast<const char*>(binary_data.data()), binary_data.size());  
auto result = stmt.execute(make_values(binary_view));  
```  
  
As long as you get a `string_view` pointing to your binary blob, you can use it for statement executions. Similarly, when reading binary data, you will get a `string_view` pointing to the retrieved binary data.  
  
So what's #105 all about?  
* It makes your intention more explicit to MySQL. It follows MySQL best practice and allows you to perform things libmysqlclient allows like inserting a string bypassing the connection's encoding. It shouldn't affect you.  
* It makes the interface easier to use.  
  
The next release will be 1.82.0, the first stable one. The library will be released with the rest of Boost, and I expect it to be available as of April 2023.
