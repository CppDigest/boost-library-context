# #242 - RediSearch response support? [Closed]

> Username: eaziz4  
> Created at: 2025-04-14 18:52:27 UTC  
> Updated at: 2025-04-27 21:47:11 UTC  
> Closed at: 2025-04-27 21:47:10 UTC  
> Url: https://github.com/boostorg/redis/issues/242  

When executing the following command, I would assume the response would be 1 element with the amount of records, and then the rest being the ids of the matching records.  I am instead getting a lot of extra nodes that just contain metadata that I do not care about(as seen in the attached picture).  How can I only return the elements that I care about(like running the same command on cli would give me) and not extra metadata?  
```  
const auto query = buildQuery(payload);  
boost::redis::request searchReq;  
searchReq.push("FT.SEARCH", "idx", query, "NOCONTENT", "LIMIT", "0", "10000");  
  
boost::redis::generic_response response;  
co_await conn->async_exec(searchReq, response, boost::asio::deferred);  
```  
  
My usecase would most likely want me to return all of my columns(20) for each key as well which gives me around 1,600,000 nodes in my response for 34,000 matching keys where so many of them are just metadata.  In production, I may have to match over 200,000 keys which would be a very large amount of data in memory and seems like I must be doing something really wrong.  This might just be my lack of knowledge around redis.  Is there a way to serialize this data into a vector of custom objects?    
  
<img width="448" alt="Image" src="https://github.com/user-attachments/assets/a71d60da-43b8-44af-a4a4-1608dc2f9413" />

---

## Comment 1

> Username: criatura2  
> Created at: 2025-04-15 08:39:37 UTC  
> Url: https://github.com/boostorg/redis/issues/242#issuecomment-2804279652  

Hi, `ft.search` has a non trivial return type and can't be mapped easily into STL containers, see here https://redis.io/docs/latest/commands/ft.search.  
  
What you can do is to understand that return type and write a function that converts it into a data structure that suits your use.

---

## Comment 2

> Username: eaziz4  
> Created at: 2025-04-16 20:00:03 UTC  
> Updated at: 2025-04-16 20:05:07 UTC  
> Url: https://github.com/boostorg/redis/issues/242#issuecomment-2810624872  

Thank you for the help!.  I'm also seeing that this request takes around .45 seconds to execute for 34,000 keys.  I'm not sure if its my redis stack configuration that is leading to it taking so long, something with how I'm executing the commands using this client library, or just the nature of my command.  I am trying to switch my datasource from parquet files with 20 columns to redis hashes and searching using an index.  I expected considerable speed improvements with this, but I'm seeing a >5x increase in time using redis.  I am simply running my redis server using docker with this command ```docker run -d --name redis-stack -p 6379:6379 redis/redis-stack-server:latest```  
  
```  
// const auto query = buildQuery(payload);  
const std::string query = "*";  
boost::redis::request searchReq;  
searchReq.push("FT.SEARCH", "betsIdx", query, "RETURN", "21",  
            "id", "devigId", "marketId", "book", "betName", "market", "selection", "odds", "line", "limit", "player", "isMainline", "isPlayerProp",  
            "link", "scraped", "eventId", "homeTeam", "awayTeam", "sport", "league", "startTime",  
            "LIMIT", "0", "200000");  
  
boost::redis::generic_response response;  
co_await conn->async_exec(searchReq, response, boost::asio::use_awaitable);  
```

---

## Comment 3

> Username: anarthal  
> Created at: 2025-04-16 21:25:05 UTC  
> Url: https://github.com/boostorg/redis/issues/242#issuecomment-2810830093  

As a random idea, you might use Wireshark with the resp3 protocol filter to see the timings of the frames and see where the slowness comes from.

---

## Comment 4

> Username: criatura2  
> Created at: 2025-04-17 06:44:44 UTC  
> Url: https://github.com/boostorg/redis/issues/242#issuecomment-2811954512  

@eaziz4 How much time does it take to execute that command with redis-cli? Does the [keys](https://redis.io/docs/latest/commands/keys/) command also take that long? If yes you can try to use [hscan](https://redis.io/docs/latest/commands/hscan/) with a cursor to see how exactly the time starts growing so much.

---

## Comment 5

> Username: eaziz4  
> Created at: 2025-04-21 18:49:37 UTC  
> Updated at: 2025-04-21 19:27:26 UTC  
> Url: https://github.com/boostorg/redis/issues/242#issuecomment-2819247327  

Sorry in advance as my redis knowledge is not great.  Here are the cli times for 55k keys.  The same command using redis-cli and trying to avoid the time cost of outputting the data takes slightly over 0.5 seconds.  This makes me think that its just an issue with the command I am running?  My use case is that I will have ~250-500k keys that have 21 field/values associated with them.  I am upserting these keys very often(every 1-2s) and need to have very fast read times on potentially the entire dataset(with optional, but VERY likely filters on ~8 fields).  I thought that storing everything as a hash and to have a redisearch index on them would make sense, but I'm now wondering if the real issue is that this is not a proper setup for my use case?  
```  
TIMEFORMAT=%R  
time redis-cli --raw FT.SEARCH betsIdx "*" RETURN 21 id devigId marketId book betName market selection odds line limit player isMainline isPlayerProp link scraped eventId homeTeam awayTeam sport league startTime LIMIT 0 200000 > /dev/null  
redis-cli --raw FT.SEARCH betsIdx "*" RETURN 21 id devigId marketId book       0.18s user 0.03s system 41% cpu 0.520 total  
```  
```  
TIMEFORMAT=%R  
time redis-cli --raw FT.SEARCH betsIdx "*" NOCONTENT LIMIT 0 200000 > /dev/null  
redis-cli --raw FT.SEARCH betsIdx "*" NOCONTENT LIMIT 0 200000 > /dev/null  0.01s user 0.01s system 27% cpu 0.073 total`  
  
```  
Using this command to retrieve all of the relevant keys(in this case, 55k keys) and pipelining it with HGETALL takes around 0.35-0.4 seconds for the HGETALLs   
```  
TIMEFORMAT=%R  
time redis-cli --raw SCAN 0 COUNT 200000 > /dev/null  
redis-cli --raw SCAN 0 COUNT 200000 > /dev/null  0.01s user 0.00s system 33% cpu 0.045 total  
```  
```  
TIMEFORMAT=%R  
time redis-cli --raw KEYS "*" > /dev/null  
redis-cli --raw KEYS "*" > /dev/null  0.01s user 0.01s system 43% cpu 0.039 total  
```  
  
> [@eaziz4](https://github.com/eaziz4) How much time does it take to execute that command with redis-cli? Does the [keys](https://redis.io/docs/latest/commands/keys/) command also take that long? If yes you can try to use [hscan](https://redis.io/docs/latest/commands/hscan/) with a cursor to see how exactly the time starts growing so much.

---

## Comment 6

> Username: anarthal  
> Created at: 2025-04-21 20:43:17 UTC  
> Url: https://github.com/boostorg/redis/issues/242#issuecomment-2819465668  

Does this mean that it's the Redis server itself what's being slow?

---

## Comment 7

> Username: eaziz4  
> Created at: 2025-04-21 21:10:52 UTC  
> Updated at: 2025-04-21 21:52:06 UTC  
> Url: https://github.com/boostorg/redis/issues/242#issuecomment-2819513147  

Yeah that is probably just the real answer and I might be wasting your time :/  Let me install boost 1.88 on an aws vm with far better network throughput than what I have currently to see a comparison.  
```  
redis-cli --latency -h 127.0.0.1 -p 6379  
min: 0, max: 14, avg: 1.28 (6996 samples)  
```  
  
Out of curiousity, I am running my redis server via the redis-stack docker image like this.  Do you have any thoughts on whether running it like this would cause performance issues as well?    
```  
docker run -d --name redis-stack-server \  
  -p 6379:6379 \  
  redis/redis-stack-server:latest  
```

---

## Comment 8

> Username: eaziz4  
> Created at: 2025-04-22 00:48:39 UTC  
> Url: https://github.com/boostorg/redis/issues/242#issuecomment-2819787751  

Running through docker on a c7i.12xlarge ec2 instance this is the latency I'm getting.  It definitely looks like running through docker is very slow   
```  
redis-cli --latency -h 127.0.0.1 -p 6379  
min: 0, max: 2, avg: 0.52 (10138 samples)   
```

---

## Comment 9

> Username: anarthal  
> Created at: 2025-04-22 07:54:17 UTC  
> Url: https://github.com/boostorg/redis/issues/242#issuecomment-2820480134  

Don't worry. When you can confirm that this is the case, let me know.

---

## Comment 10

> Username: eaziz4  
> Created at: 2025-04-24 22:25:22 UTC  
> Updated at: 2025-04-24 22:33:26 UTC  
> Url: https://github.com/boostorg/redis/issues/242#issuecomment-2828994128  

I have my redis server hosted on an r8g.2xlarge ec2 instance and my client is a c7i.12xlarge ec2 instance.  With 104k keys, these are my latencies for these commands and a redis benchmarket for get  
```  
redis-benchmark -h 10.0.0.230 -q -t get -r 100000 -n 1000000  
GET: 136761.48 requests per second, p50=0.183 msec  
  
redis-benchmark -h 10.0.0.230 -q -t get -P 16 -r 100000 -n 1000000  
GET: 1044932.06 requests per second, p50=0.727 msec  
```  
```  
const std::string query = "*";  
boost::redis::request searchReq;  
searchReq.push("FT.SEARCH", "betsIdx", query, "NOCONTENT", "LIMIT", "0", "250000");  
  
boost::redis::generic_response response;  
const auto searchStart = std::chrono::high_resolution_clock::now();  
co_await conn->async_exec(searchReq, response, boost::asio::deferred);  
const auto searchStop = std::chrono::high_resolution_clock::now();  
const auto searchDuration = std::chrono::duration_cast<std::chrono::milliseconds>(searchStop - searchStart);  
std::cout << "FT.SEARCH: " << std::to_string(searchDuration.count()/1000.0) << " seconds\n";  
```  
FT.SEARCH: 0.203000 seconds  
  
```  
const auto& raw = response.value();  
std::size_t i = 11;  
boost::redis::request hgetRequest;  
while (i < raw.size() - 1) {  
    hgetRequest.push("HGETALL", raw[i].value);  
    i += 3;  
}  
boost::redis::generic_response hgetResponse;  
const auto hgetStart = std::chrono::high_resolution_clock::now();  
co_await conn->async_exec(hgetRequest, hgetResponse, boost::asio::deferred);  
const auto hgetStop = std::chrono::high_resolution_clock::now();  
const auto hgetDuration = std::chrono::duration_cast<std::chrono::milliseconds>(hgetStop - hgetStart);  
std::cout << "HGETALL: " << std::to_string(hgetDuration.count()/1000.0) << " seconds\n";  
```  
HGETALL: 0.267000 seconds  
  
Total Time: 0.47 seconds  
  
This still feels very high for what I want to do but it looks like its an issue with how I'm storing my data in redis as I have 9 possible filterable fields on my index and 21 fields in each hash.

---

## Comment 11

> Username: anarthal  
> Created at: 2025-04-27 21:47:10 UTC  
> Url: https://github.com/boostorg/redis/issues/242#issuecomment-2833654767  

If you're okay with it, I will be closing this issue now. If you need any more help, please feel free to open another one.  
  
Cheers,  
Ruben.
