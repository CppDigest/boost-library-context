# #162 - End of file [asio.misc:2] & Mismatched sequence numbers [mysql.client:5] [Closed]

> Username: kylindai  
> Created at: 2023-06-14 08:10:53 UTC  
> Updated at: 2023-06-22 14:32:03 UTC  
> Closed at: 2023-06-22 14:32:03 UTC  
> Url: https://github.com/boostorg/mysql/issues/162  

When I update my table in multi-thread way, I always get the following error msgs:  
  
_mysql error_code = End of file [asio.misc:2]  
mysql error_code = Mismatched sequence numbers [mysql.client:5]_  
  
And I have closed the statement by invoke conn.close_statement() after each calling conn.execute(stmt.bind(...), dig, ec);  
  
Some advice could give me?  Thanks a lot~

---

## Comment 1

> Username: anarthal  
> Created at: 2023-06-14 10:54:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/162#issuecomment-1590963653  

Hi @kylindai,  
  
This looks like a race condition on a `connection` object. By design, connections are not thread-safe, and impose the condition on having a single outstanding network operation at a time.  
  
Some questions to help you move forward:  
  
* How are you protecting `connection`? Are you using `boost::asio::strand` or similar?  
* Are you using sync or async functions?  
* Are you creating one or several connections?  
* Does your code guarantee that networking operations do not overlap for a single connection instance?  
  
Regards,  
Ruben.

---

## Comment 2

> Username: kylindai  
> Created at: 2023-06-15 03:03:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/162#issuecomment-1592270447  

Thanks for getting your reply so soon.  
  
Yes, it's an usual connection pool implement, pre-creating a group of connections in a vector, and each conn has it's own io_context, and pre-connect the mysql server, when the operation finished,   not close the connection really.  
  
Is this a wrong usage? Any idea for creating connection pool of using this library?   
Thanks a lot~

---

## Comment 3

> Username: anarthal  
> Created at: 2023-06-16 14:17:28 UTC  
> Updated at: 2023-06-16 14:17:58 UTC  
> Url: https://github.com/boostorg/mysql/issues/162#issuecomment-1594759847  

Hi @kylindai,  
  
The pattern you describe is a valid usage of the library, as long as you can guarantee that only one operation is outstanding for a given connection at a single time. I've got the feeling that your code is failing to guarantee this condition.  
  
If you could somehow share me your connection pool code, I can assist you to find whether this is the case or not.  
  
Edit: are you using sync or async functions?  
  
Regards,  
Ruben.

---

## Comment 4

> Username: kylindai  
> Created at: 2023-06-18 06:00:05 UTC  
> Updated at: 2023-06-18 06:00:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/162#issuecomment-1595974535  

Hi @anarthal   
  
Thanks for your reply, I have got my mistake, it is a bug in my codes:  
  
T& get_conn() {  
    std::lock_guard<std::mutex> lock(lock_);    
    return conn_holder->get();   // here, many threads could get the same conn that not been released  
}  
  
bool release(T& conn) {  
    std::lock_guard<std::mutex> lock(lock_);  
    return conn_holder->release(conn);  
}  
  
The library is excellent !!! This issue could be closed, thanks.

---

## Comment 5

> Username: anarthal  
> Created at: 2023-06-22 14:32:03 UTC  
> Url: https://github.com/boostorg/mysql/issues/162#issuecomment-1602747512  

Glad you could find it. I'm closing this issue now. If you have any further questions, please ask.
