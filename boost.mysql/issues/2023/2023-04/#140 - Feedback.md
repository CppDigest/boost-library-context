# #140 - Feedback [Open]

> Username: anarthal  
> Created at: 2023-04-05 09:10:09 UTC  
> Updated at: 2025-11-15 11:58:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/140  

* Are you finding any problems when using the library?  
* Is there any use case that we're not covering as you'd like?  
* Do you have any feature suggestion?  
* Are you successfully using the library and you'd like to share your use case with us?  
  
Comment on this issue if you'd like to share any feedback.

---

## Comment 1

> Username: SpareSimian  
> Created at: 2025-02-12 03:09:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/140#issuecomment-2652555703  

I'm pretty happy with this library. It got me to learn about ASIO coroutines in C++. The tutorials here would make a great introduction for a general C++ coroutine class. I'd suggest pushing the tutorials more in the README.   
  
Is anyone using a C++ library to generate SQL statements? Something that will do a bit of SQL syntax checking in the compiler before it gets checked on the server at runtime. Some hints at candidates:  
https://stackoverflow.com/questions/18346357/c-sql-query-building-library  
https://www.reddit.com/r/cpp/comments/hxy27v/a_singleheader_library_for_build_sql_query_string/  
https://github.com/six-ddc/sql-builder

---

## Comment 2

> Username: anarthal  
> Created at: 2025-02-12 19:08:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/140#issuecomment-2654616327  

Thanks for your feedback! Writing these took me more than I anticipated, so this is comforting to hear. Mentioning #166 to update the readme by linking the tutorials.  
  
The first link you provide is very similar to the client-side SQL formatting we already have here - from the SO answer, it looks like no compile-time checks are happening there. Docs about client-side SQL formatting:  
* Basic: https://www.boost.org/doc/libs/1_87_0/libs/mysql/doc/html/mysql/sql_formatting.html  
* Advanced: https://www.boost.org/doc/libs/1_87_0/libs/mysql/doc/html/mysql/sql_formatting_advanced.html  
  
I'm not sure how viable is to syntax check everything client-side, since this would require a SQL parser in the client, and MySQL's grammar is massive (and it actually changes from version to version). It's probably more viable to go with an approach like the one in the 2nd link. This feature is out of scope for this library, but I could think of writing something like it as a different library (although not right now as I'm with other stuff). It can be implemented on top of `format_context` and `format_sql_to`.

---

## Comment 3

> Username: SpareSimian  
> Created at: 2025-02-12 20:54:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/140#issuecomment-2654822613  

I came across this library that does compile-type SQL syntax checking but it relies on its own connector library for connecting to various databases: https://github.com/rbock/sqlpp11  
There's been a request to add async capability: https://github.com/rbock/sqlpp11/issues/344  
I'm wondering how hard it might be to write a connector implementation using Boost::MySQL.

---

## Comment 4

> Username: anarthal  
> Created at: 2025-02-13 18:20:36 UTC  
> Url: https://github.com/boostorg/mysql/issues/140#issuecomment-2657399918  

That can be done, but the result will still be sync. As I see it, sqlpp11 is like an "interface", with individual connectors implementing that interface. If the interface is sync, even if individual connectors have async capabilities, the result will still be sync.  
  
I think an async version of sqlpp11 would be useful. It's something I have in mind in the long run. I'm currently implementing a postgres equivalent of Boost.MySQL. With two async DB libraries, we could think of an interface that makes sense.

---

## Comment 5

> Username: Riztazz  
> Created at: 2025-11-15 09:22:44 UTC  
> Updated at: 2025-11-15 09:23:07 UTC  
> Url: https://github.com/boostorg/mysql/issues/140#issuecomment-3536246174  

Anyway to track the progress on the postgres equivalent?:P  
Love the lib, thanks for it!

---

## Comment 6

> Username: anarthal  
> Created at: 2025-11-15 11:58:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/140#issuecomment-3536418450  

Actually yes :) https://github.com/anarthal/nativepg
