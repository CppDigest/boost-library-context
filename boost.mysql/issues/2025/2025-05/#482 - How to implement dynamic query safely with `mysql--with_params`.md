# #482 - How to implement dynamic query safely with `mysql::with_params`? [Closed]

> Username: Eggache666  
> Created at: 2025-05-29 06:44:23 UTC  
> Updated at: 2025-06-01 02:39:19 UTC  
> Closed at: 2025-06-01 02:39:18 UTC  
> Url: https://github.com/boostorg/mysql/issues/482  

To avoid SQL injection, I need to use parameterized queries with mysql::with_params. How can I rewrite dynamic query safely:  
```  
void query(const json& args) {  
  auto sql = std::string("select * from xx where A = 'a' ");  
  if (args.contains("B")  
    sql += std::format("and B = {} ", args["A"]);  
  
  if (args.contains("C")  
    sql += std::format("and C = {} ", args["C"]);  
  
  if (args.contains("D")  
    sql += std::format("and D != {} ", args["D"]);  
  ....  
}  
```  
  
Using std::format makes it easy but not safe, and mysql::with_params does’t support, so how can I implement such dynamic SQL with mysql::with_params? thanks!

---

## Comment 1

> Username: anarthal  
> Created at: 2025-05-29 12:56:35 UTC  
> Url: https://github.com/boostorg/mysql/issues/482#issuecomment-2919311895  

Hi,  
  
You want `mysql::format_context` and `mysql::format_sql_to`. `mysql::with_params` is just a wrapper around them. Docs: https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/sql_formatting_advanced.html  
  
For example:  
  
```cpp  
void do_query(mysql::any_connection& conn, const json& args)  
{  
    // format_context will accumulate the query as we compose it  
    mysql::format_context ctx(conn.format_opts().value());  
  
    // format_sql_to is roughly equivalent to your += std::format(...), but secure  
    if (args.contains("B"))  
        mysql::format_sql_to(ctx, "and B = {} ", args["A"]);  
  
    if (args.contains("C"))  
        mysql::format_sql_to(ctx, "and C = {} ", args["C"]);  
  
    if (args.contains("D"))  
        mysql::format_sql_to(ctx, "and D != {} ", args["D"]);  
  
    // When calling get().value(), you get a std::string that you may now pass to execute() or async_execute()  
    std::string query = std::move(ctx).get().value();  
}  
```  
  
Some considerations regarding error handling (`with_params` handles these for you, but you need to do them manually here):  
* `conn.format_opts()` returns a `system::result<mysql::format_options>`. This includes stuff like the character set that your connection is using. `conn.format_opts().value()` will throw an exception if the connection doesn't know what character set is using (it shouldn't happen in general).  
* Formatting might fail, for example if any of the strings you pass as argument are not correctly encoded (e.g. if you're using UTF-8, and `args["C"]` contains invalid UTF-8, formatting will fail). This means that `std::move(ctx).get()` will return a `system::result<std::string>` containing an error. `std::move(ctx).get().value()` will throw an exception.  
  
If you don't like exceptions, you can manually check `system::result<T>::has_error()` in a conditional.  
  
Hope this helps.  
  
Regards,  
Ruben.

---

## Comment 2

> Username: Eggache666  
> Created at: 2025-06-01 02:39:18 UTC  
> Url: https://github.com/boostorg/mysql/issues/482#issuecomment-2926318622  

That's what I want,  the example provided a very detailed answer to my question. thank you for your help!  
  
Best wishes!
