# #458 - [Question] is there a benchmark/comparison test to the standard libmysqlclient available? [Closed]

> Username: LowLevelMahn  
> Created at: 2025-02-27 13:55:40 UTC  
> Updated at: 2025-08-06 10:18:58 UTC  
> Closed at: 2025-04-02 09:32:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/458  

not the speed of the database itself but the overhead of protocol-handling etc. in speed and memory usage?

---

## Comment 1

> Username: anarthal  
> Created at: 2025-02-27 15:11:20 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2688251118  

Hi!  
  
Not right now, although I'd like to add one. Are you aware of any standard tests I could perform? Or otherwise any specific comparison you'd like to see?  
  
The points I don't see clear (and why I haven't done it yet):  
* Should we use text queries or prepared statements? The extra round-trip of statement preparation/execution, which you can easily avoid using `with_params`.  
* Should we include session establishment overhead somehow? Part of this overhead can be avoided with`connection_pool`.  
* Finally, should we use sync functions, async functions using callbacks, or async functions using coroutines? I'd inclide towards the last one.  
  
I'm happy to hear your suggestions.  
  
Thanks,  
Ruben.

---

## Comment 2

> Username: LowLevelMahn  
> Created at: 2025-02-27 15:31:45 UTC  
> Updated at: 2025-02-27 19:23:16 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2688313667  

> Are you aware of any standard tests I could perform? Or otherwise any specific comparison you'd like to see?  
  
any standard test i found are db-performance related - not the performance of the protocol encoding/decoding  
in principle is it encoding-speed: large queries (with many parameter with large values , strings in select etc.) for prepared statements  
and queries with large (many rows/cols, datasize) resultset (decoding-speed)  
  
(maybe only using temporary tables - or what is best to keep the db performance out of the test)  
Test 1: with libmysqlclient -> this is the Oracle-Speed  
Test 2: the same queries using Boost.Mysql  
  
additionally on procotol-level (no connection to the DB, just the buffers with binary data grabbed from Test 2 as input or regression test) just to check how fast the encoding/decoding behaves (if that is relevant at all)  
  
> Should we use text queries or prepared statements?  
  
both but i think the prepared statements are more encoding speed relevant  
  
the additional tests could use the Test1/2 queries  
  
the problem is to keep the DB mostly out of the test - so maybe a temporary/virtual table or something were the speed is more constant

---

## Comment 3

> Username: anarthal  
> Created at: 2025-03-04 19:17:35 UTC  
> Updated at: 2025-03-04 19:17:51 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2698672574  

I've been performing some benchmarks regarding data retrieval. Test data looks like this:  
  
```sql  
CREATE TABLE test_data(  
    id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,  
    s8_0 TINYINT NOT NULL,  
    u8_0 TINYINT UNSIGNED NOT NULL,  
    s16_0 SMALLINT NOT NULL,  
    u16_0 SMALLINT UNSIGNED NOT NULL,  
    s32_0 INT NOT NULL,  
    u32_0 INT UNSIGNED NOT NULL,  
    s64_0 BIGINT NOT NULL,  
    u64_0 BIGINT UNSIGNED NOT NULL,  
    s1_0 VARCHAR(256),  
    s2_0 TEXT,  
    b1_0 VARBINARY(256),  
    b2_0 BLOB,  
    flt_0 FLOAT,  
    dbl_0 DOUBLE,  
    dt_0 DATE,  
    dtime_0 DATETIME,  
    t_0 TIME  
);  
  
INSERT INTO test_data(  
    s8_0, u8_0, s16_0, u16_0, s32_0, u32_0, s64_0, u64_0, s1_0, s2_0, b1_0, b2_0, flt_0, dbl_0, dt_0, dtime_0, t_0  
)  
WITH RECURSIVE cte AS (  
    SELECT 0 num  
    UNION ALL  
    SELECT num + 1 FROM cte  
    WHERE num < 10000  
) SELECT   
    FLOOR(RAND()*(0x7f+0x80+1)-0x80),  
    FLOOR(RAND()*(0xff+1)),  
    FLOOR(RAND()*(0x7fff+0x8000+1)-0x8000),  
    FLOOR(RAND()*(0xffff+1)),  
    FLOOR(RAND()*(0x7fffffff+0x80000000+1)-0x80000000),  
    FLOOR(RAND()*(0xffffffff+1)),  
    FLOOR(RAND()*(0x7fffffffffffffff+0x8000000000000000)-0x7fffffffffffffff),  
    FLOOR(RAND()*(0xffffffffffffffff)),  
    REPEAT(UUID(), 5),  
    REPEAT(UUID(), FLOOR(RAND()*(1500-1000+1)+1000)),  
    REPEAT(UUID(), 5),  
    REPEAT(UUID(), FLOOR(RAND()*(1500-1000+1)+1000)),  
    RAND(),  
    RAND(),  
    CURDATE(),  
    CURTIME(),  
    SEC_TO_TIME(RAND() + FLOOR(RAND()*(839*3600+839*3600+1)-839*3600))  
FROM cte;  
```  
  
That is, a table with ~20 fields covering most data types, including long strings and blobs, and with 10000 rows. I couldn't make this a memory-only temporary table because these don't support TEXT/BLOB columns. For now, I've performed two tests, both with prepared statements:  
  
* Repeatedly querying a single row by ID.  
* Querying the entire table.  
  
Tests run with TLS disabled and using UNIX sockets to minimize network stack impact. I've used sync functions in both cases.  
  
Boost.MySQL slightly outperforms libmysqlclients in both tests, although the results are likely not statistically significant. Some numbers (times in milliseconds):  
  
```  
many_rows_libmysqlclient  
2458  
2386  
2374  
2294  
2405  
2370  
2409  
2339  
2312  
2365  
many_rows_boost  
2376  
2299  
2343  
2299  
2318  
2322  
2297  
2359  
2299  
2302  
one_row_libmysqlclient  
954  
893  
930  
903  
923  
975  
890  
893  
877  
887  
one_row_boost  
878  
841  
914  
859  
849  
853  
855  
897  
846  
849  
```  
  
I don't think that encoding/decoding here is by any means the bottleneck here. Statements use a binary protocol, where deserializing types is almost trivial. I can't do a comparison of the speed of raw deserialization functions because libmysqlclient does not expose them.  
  
I still need to write benchmarks for sending data to the server (e.g. prepared statements with many/long parameters).  
  
Before I go ahead and clean up the code and integrate it in CIs, is this the kind of benchmark that you'd like to see?  
  
Thanks,  
Ruben.

---

## Comment 4

> Username: LowLevelMahn  
> Created at: 2025-03-04 19:43:55 UTC  
> Updated at: 2025-03-04 19:44:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2698736484  

> That is, a table with ~20 fields covering most data types, including long strings and blobs, and with 10000 rows. I couldn't make this a memory-only temporary table because these don't support TEXT/BLOB columns. For now, I've performed two tests, both with prepared statements:  
  
that is a good start - and your data could create a nice graphic :)  
  
> I don't think that encoding/decoding here is by any means the bottleneck here. Statements use a binary protocol, where deserializing types is almost trivial. I can't do a comparison of the speed of raw deserialization functions because libmysqlclient does not expose them.  
  
i once checked a serialize lib at a customer and found that there were hidden allocations and virtualization etc. that just took a decent amount time without any real benefit - hope your (de)serializer is some sort zero-copy/allocation avoiding strategy based  
  
> I still need to write benchmarks for sending data to the server (e.g. prepared statements with many/long parameters).  
  
i hope your c++ inline code just gets better optimized, reducing the overall calling overhead  
compared to non-inline-able C function calls of libmysql  
  
> Before I go ahead and clean up the code and integrate it in CIs, is this the kind of benchmark that you'd like to see?  
  
yes - that test is filling a gap for me and makes the project more professional  
  
btw: how do you benchmark your code? VTune, Profile, Flamegraphs?

---

## Comment 5

> Username: anarthal  
> Created at: 2025-03-04 21:11:25 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2698916771  

> that is a good start - and your data could create a nice graphic :)  
  
Yes, I'm conscious that there's still a lot to do. My aim is to create a docs page with the full comparison.  
  
> i once checked a serialize lib at a customer and found that there were hidden allocations and virtualization etc. that just took a decent amount time without any real benefit - hope your (de)serializer is some sort zero-copy/allocation avoiding strategy based  
  
Serialization uses a single, contiguous, resizable network buffer that gets reused at each operation, effectively providing amortized zero allocations on serialization. The buffer makes some clever optimizations to save space for protocol headers, since I found out that making frames contiguous was faster than using vectored I/O. with_params has knowledge of the buffer and expands queries to the buffer directly.  
  
Deserialization is non-allocating, based on views. Frames (e.g. rows and other messages) are first read into a resizable buffer, then parsed. Parsing functions always use views, and use no virtualization. When using results/execute, data is copied a single time into the results object, similar to how libmysqlclient operates. When using execution_state/start_execution, no copies of the values are made, and pointers to the network buffers are used.  
  
> i hope your c++ inline code just gets better optimized, reducing the overall calling overhead  
compared to non-inline-able C function calls of libmysql  
  
The numbers I showed you use BOOST_MYSQL_SEPARATE_COMPILATION, so no such benefit should be observed. The overhead of a function call is neglegible in comparison to the execution of a query. It could only be noticeable if the serialization functions used virtualization, but they don't.  
  
> btw: how do you benchmark your code? VTune, Profile, Flamegraphs?  
  
I tend to use callgrind which generates call graphs. In general, you always get that syscalls are what dominates execution times by far, so most of my recent optimization work has been directed towards reducing round-trips to the server, rather than the kind of optimizations you'd find in a library like Boost.Container (that doesn't prevent me from trying to save as much as I can). Examples of this are connection_pool (aimed at saving session re-establishments), pipelines and with_params (saves a round-trip vs. preparing and executing a statement).

---

## Comment 6

> Username: LowLevelMahn  
> Created at: 2025-03-05 13:52:23 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2701009123  

so you seem very well prepared for maximum speed - as long as the DB doesn't slow the whole show :)  
  
> I tend to use callgrind which generates call graphs. In general, you always get that syscalls are what dominates execution times by far  
  
ever tried VTune for Windows/Linux? i really like that free tool from Intel - the summaries are very good to read

---

## Comment 7

> Username: anarthal  
> Created at: 2025-03-09 19:40:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2709036012  

>so you seem very well prepared for maximum speed - as long as the DB doesn't slow the whole show :)  
  
there's a reason why I'm writing a postgres driver now :)  
  
> ever tried VTune for Windows/Linux? i really like that free tool from Intel - the summaries are very good to read  
  
I haven't, but it looks pretty good. I'll have it a try this incoming week as I write the other benchmarks. Thanks for sharing it.

---

## Comment 8

> Username: LowLevelMahn  
> Created at: 2025-03-10 07:14:25 UTC  
> Updated at: 2025-03-10 07:32:27 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2709632651  

> > so you seem very well prepared for maximum speed - as long as the DB doesn't slow the whole show :)  
>   
> there's a reason why I'm writing a postgres driver now :)  
>   
  
and MSSQL? there is already [FreeTDS](https://www.freetds.org/) but thats not Boost :)   
  
and Oracle?   
 - [TNS: Transparent Network Substrate](https://en.wikipedia.org/wiki/Transparent_Network_Substrate)  
 - [Repost: Oracle Protocol](https://www.pythian.com/blog/technical-track/repost-oracle-protocol)  
  
just because there isn't a single open source project for this :)  
  
> > ever tried VTune for Windows/Linux? i really like that free tool from Intel - the summaries are very good to read  
>   
> I haven't, but it looks pretty good. I'll have it a try this incoming week as I write the other benchmarks. Thanks for sharing it.  
  
very interested in your feedback

---

## Comment 9

> Username: anarthal  
> Created at: 2025-03-10 16:20:56 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2711141528  

I had MSSQL in mind too, but if I need to choose, I tend to prefer postgres.  
  
> and Oracle?  
  
I was convinced there were no resources on this, so this is something really to consider. If we happened to have all four connectors, a truly async Boost.SQL becomes possible. Thanks for sharing.

---

## Comment 10

> Username: LowLevelMahn  
> Created at: 2025-03-11 06:02:34 UTC  
> Updated at: 2025-03-11 06:03:36 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2712769370  

> I was convinced there were no resources on this, so this is something really to consider.  
  
the comment from "Bob Brownell" on [Repost: Oracle Protocol](https://www.pythian.com/blog/technical-track/repost-oracle-protocol) is a little bit strange - ftp-download+password for NetDataLite - found the nearly exact same comment also on Stackoverflow regarding Oracle Protocol disecting - and there is a "Bob Brownell" who is related to the product NetData(Lite) - maybe he thinks its just "semi" suspicious to give out ftp server links with passwords in comments :)  
  
i would try to reach him first - maybe there is even more up-to-date information available - if you ever get to implementing TNS

---

## Comment 11

> Username: anarthal  
> Created at: 2025-03-28 19:15:49 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2762224830  

Sorry for taking this long to come back. I've finally been able to run proper benchmarks (see the PR linked to this issue for benchmark code) and do some analysis. This figure summarizes my findings (I'll add it to the docs):  
  
![Image](https://github.com/user-attachments/assets/92d36508-e5f6-4938-b8be-523f7852f514)  
  
TL;DR: we score slightly better than official drivers in most benchmarks.  
  
The MariaDB driver performs slightly better in the one row benchmark, although with more dispersion. Run with gcc-12 under Ubuntu 24.04, using CMake Release config, with a MySQL 8.4.1 server in localhost, using UNIX sockets to minimize the time taken by communication. I've run each benchmark 10 times for the figure.  
  
I've also profiled the three benchmarks with vtune (thanks for the suggestion, it's much better than callgrind) and I've identified 3 potential optimizations that we could do:  
  
* #461. This affects only the single row benchmarks and is trivial to do.  
* #462. This affects only the single row benchmarks too, because I used very big rows. This one is more involved.  
* Statements params are copied into the network buffer when serialized. If very big strings are passed, this has some overhead that shows up in the profiler. We could try to use vector I/O here. This can be done transparently for sync functions, but requires collaboration from the user for async ones. Async functions do not require the passed parameters to be kept alive after initiation, so changing this would be breaking the API. I could consider introducing a marker `mysql::dont_copy` type for this. I'd like to hear your opinion about this before moving on.  
  
Cheers,  
Ruben.

---

## Comment 12

> Username: LowLevelMahn  
> Created at: 2025-03-29 13:19:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2763360920  

> The MariaDB driver performs slightly better in the one row benchmark, although with more dispersion.  
  
that why i talked about an additional test - only encode/decoding real world scenarios like in your current test - to keep the database out of the way - but there is still asio... do you think the 3 optimizations will bring you also on par with mariadb on the single row scenarios?  
  
> I've also profiled the three benchmarks with vtune (thanks for the suggestion, it's much better than callgrind)   
  
great, i hoped it would help you  
  
> * Statements params are copied into the network buffer when serialized. If very big strings are passed, this has some overhead that shows up in the profiler. We could try to use vector I/O here. This can be done transparently for sync functions, but requires collaboration from the user for async ones.  
  
so its about the buffer that needs to be available until send and currently you handling all that for the users and to optimize that you need to led more control in the users hands about the lifetime?  
  
all in all great results - your lib is prooved fast :)

---

## Comment 13

> Username: anarthal  
> Created at: 2025-03-29 16:51:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-2763714120  

> that why i talked about an additional test - only encode/decoding real world scenarios like in your current test - to keep the database out of the way - but there is still asio... do you think the 3 optimizations will bring you also on par with mariadb on the single row scenarios?  
  
The problem is that neither libmysqlclient nor libmariadb expose any serialization primitives. Also, bear in mind that (de)serialization isn't involved in neither #461 nor #462 - so tests not involving the entire system wouldn't have caught this.  
  
Surprisingly, Asio doesn't show up in vtune at all. It looks like the sync primitives are very optimized, and since it's header-only, we get no overhead here. In async it would probably show up, though, since Asio does more things there. It would be difficult to compare there, though, since libmariadb's async mode requires invoking primitives like epoll yourself, and libmysqlclient's directly require you to busy wait.  
  
> do you think the 3 optimizations will bring you also on par with mariadb on the single row scenarios?  
  
I don't think the difference we're observing there is statistically significant. #461 and #462 may yield some improvements, although I don't know if they will be enough to make the difference significant in our favor. I'm already working on #461 because it's easy, it may take more time for me to land the other one.  
  
> so its about the buffer that needs to be available until send and currently you handling all that for the users and to optimize that you need to led more control in the users hands about the lifetime?  
  
Imagine this situation:  
  
```cpp  
std::string get_name();  
  
class session {  
    mysql::results r_;  
public:  
    void create_employee(mysql::any_connection& conn, mysql::statement stmt) {  
        conn.async_execute(stmt.bind(get_name()), r_, [](...) { /* do whatever */ });  
    }  
};  
```  
  
We currently guarantee that this will work, even if the string you're passing there (the one returned by `get_name`) ends its lifetime before the callback is invoked. This is fine for small/medium strings, which can be copied into the network buffer avoiding vector I/O altogether.  
  
If we want to implement a no-copy approach here, we need some cooperation from the user. Something like:  
  
```cpp  
std::string get_name(); // imagine that this string may be very long  
  
class session {  
    mysql::results r_;  
    std::string name_;  
public:  
    void create_employee(mysql::any_connection& conn, mysql::statement stmt) {  
        name_ = get_name();  
        // By using dont_copy, I promise you very hard that name_ will be alive until the callback is called.  
        // This way, we can avoid copying the string at all, and use vector I/O instead  
        conn.async_execute(stmt.bind(mysql::dont_copy(name_)), r_, [](...) { /* do whatever */ });  
    }  
};  
```

---

## Comment 14

> Username: LowLevelMahn  
> Created at: 2025-08-01 09:22:20 UTC  
> Updated at: 2025-08-01 09:25:43 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-3143865874  

(im currently working on optimizing a Asio based communication library - that reminded me of this discussion)  
  
do you use your own allocators for post/read/write handles?  it helped me to reduced the latency (by reducing allocations using a freelist) some percents in my scenario  
  
Any latency war-stories? :)  
  
this is a minimal example for allocation replament in context of asio::post - still using new/delete  
  
```  
#include <boost/asio.hpp>  
#include <iostream>  
#include <memory>  
  
// Simple Allocator example based on goold-old new/delete  
template<typename T>  
struct SimpleAllocator {  
    using value_type = T;  
  
    SimpleAllocator() = default;  
  
    template<class U>  
    constexpr SimpleAllocator(const SimpleAllocator<U>&) noexcept {}  
  
    T* allocate(std::size_t n) {  
        std::cout << "Allocating " << n << " object(s)\n";  
        return static_cast<T*>(::operator new(n * sizeof(T)));  
    }  
  
    void deallocate(T* p, std::size_t n) noexcept {  
        std::cout << "Deallocating " << n << " object(s)\n";  
        ::operator delete(p);  
    }  
};  
  
template<class T, class U>  
bool operator==(const SimpleAllocator<T>&, const SimpleAllocator<U>&) { return true; }  
  
template<class T, class U>  
bool operator!=(const SimpleAllocator<T>&, const SimpleAllocator<U>&) { return false; }  
  
template<typename Handler>  
class AllocHandler {  
public:  
    using allocator_type = SimpleAllocator<void>;  
  
    AllocHandler(Handler h) : handler_(std::move(h)) {}  
  
    allocator_type get_allocator() const noexcept {  
        return allocator_type{};  
    }  
  
    void operator()() {  
        handler_();  
    }  
  
private:  
    Handler handler_;  
};  
  
template<typename Handler>  
AllocHandler<Handler> make_alloc_handler(Handler h) {  
    return AllocHandler<Handler>(std::move(h));  
}  
  
int main() {  
    boost::asio::io_context io;  
  
    for (size_t i = 0; i < 10; ++i)  
    {  
        boost::asio::post(io, make_alloc_handler([i] {  
            std::cout << "Hello from custom allocator handler" << i << "!\n";  
        }));  
    }  
  
    io.run();  
  
    return 0;  
}  
  
```

---

## Comment 15

> Username: anarthal  
> Created at: 2025-08-01 15:39:50 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-3144994379  

Not many stories. I can get some tips though:  
  
- Going through the event loop is always heavier than not doing so. For instance, when using channels under heavy load, try_receive will always be faster than async_receive  
- Dispatch is faster than post because dispatch handlers might be called inline, while post handlers always return to the event loop. Post exists to prevent stack exhaustion, and all Asio compliant operations use semantics equivalent to post. That is, on immediate completion (when data is ready without suspension), a post is issued. You can suppress this using bind_immediate_executor.  
- Boost.Cobalt coroutines (and NOT Boost.Asio ones) are capable of ignoring part of this rescheduling overhead using symmetric transfer  
- This article summarizes what I said: https://github.com/boostorg/redis/blob/develop/doc/on-the-costs-of-async-abstractions.md  
- You can use bind_allocator to simplify your example  
- You might want to try recycling_allocator for your handlers, which is shipped with Asio, and see if it improves your performance.  
- Multi-threading and strands come with a cost, so use them wisely and measure

---

## Comment 16

> Username: LowLevelMahn  
> Created at: 2025-08-02 09:45:35 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-3146396665  

thanks you for the list

---

## Comment 17

> Username: LowLevelMahn  
> Created at: 2025-08-06 10:18:58 UTC  
> Url: https://github.com/boostorg/mysql/issues/458#issuecomment-3159185292  

im experiencing performance drop over our own stupid (but well optimized) select windows/linux backend (using own own queues for jobs etc.) - its extremly hard to reach that speed if queue performance is relevant in a test-case - we try to go with our own queue now  
  
the problem with all these librarys/benchmarks is that there are no "prooved" implementations available (for example echo client/server) and bare-to-the-metal ones so its often only a "feeling" what speed, low-latency, cpu-load, put-through performance is reachable in descent scenarios
