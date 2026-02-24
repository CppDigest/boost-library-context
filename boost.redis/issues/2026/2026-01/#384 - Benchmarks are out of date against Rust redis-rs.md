# #384 - Benchmarks are out of date against Rust redis-rs [Open]

> Username: BijanVan  
> Created at: 2026-01-19 20:08:57 UTC  
> Updated at: 2026-01-24 20:13:08 UTC  
> Url: https://github.com/boostorg/redis/issues/384  

It seems [benchmarks](https://www.boost.org/doc/libs/latest/libs/redis/doc/html/redis/benchmarks.html) are out of date at least against Rust redis-rs.  
  
boost::redis:  
https://godbolt.org/z/MYTqW5MY8  
  
Rust redis-rs  
https://godbolt.org/z/v5dxsYnoE  
  
  
In an EC2 machine:  
  
<img width="705" height="238" alt="Image" src="https://github.com/user-attachments/assets/75053ae1-2260-45aa-9f2e-9bfd40053d3f" />

---

## Comment 1

> Username: nihohit  
> Created at: 2026-01-19 22:07:13 UTC  
> Url: https://github.com/boostorg/redis/issues/384#issuecomment-3770268211  

I'd argue that the benchmarks are just plain wrong - tasks don't run concurrently, but serially. Why bother with an async interface if nothing is concurrent?

---

## Comment 2

> Username: mzimbres  
> Created at: 2026-01-20 20:39:13 UTC  
> Url: https://github.com/boostorg/redis/issues/384#issuecomment-3774843497  

Hi @BijanVan , as far as I can see you are not running the same benchmark as I did (see the benchmarks folder).  
  
That sad, those results are almost four years old and I agree an update is needed. In fact, I am working on it at the moment on my https://github.com/mzimbres/redis-cli-comp repository, which is still work in progress and therefore I haven't advertised it yet.  
  
Redis-rs seems to have improved a lot, but the performance difference between boost-redis and redis-rs seems to persist to this day. After seeing your message I have rerun the benchmarks from my new repo and got the following results  
  
- Commands per second  
  
Client         | Megahertz  
---------------|-------------------  
boost_redis_co | 6578  
boost_redis_cb | 8207  
redis_rs       | 1470  
go_redis       | 1581  
  
- Cpu usage  
  
Client         | %usr   | %system | %Total  
---------------|--------|---------|-------  
boost_redis_co |  92.00 |   7.33  |  99.33  
boost_redis_cb |  92.43 |   4.86  |  97.29  
redis_rs       | 199.72 |  84.28  | 284.00  
go_redis       | 211.25 | 140.83  | 352.08  
  
- Resource usage  
  
Type           | threads | fd-nr  
---------------|---------|------  
boost_redis_co |    2    |    7    
boost_redis_cb |    2    |    7    
redis_rs       |   21    |   10    
go_redis       |   30    | 1006    
  
The general feeling is that rueidis comes 3rd in terms of performance after boost-redis and rueidis, the latter is not included above because of a problem in my script.  
  
I agree with @nihohit  in the sense that your benchmarks do not seem to measure a useful quantity from the practical point of view, I think it will be dominated by the unavoidable latency i.e. write/ read socket, Redis processing time etc. Let me know if they represent a practical use case and we can review your benchmark code and perhaps optimize boost-redis for it.  
  
For reference: https://github.com/redis-rs/redis-rs/issues/637

---

## Comment 3

> Username: nihohit  
> Created at: 2026-01-20 20:52:26 UTC  
> Updated at: 2026-01-20 20:52:54 UTC  
> Url: https://github.com/boostorg/redis/issues/384#issuecomment-3774890850  

@mzimbres https://github.com/redis-rs/redis-rs/issues/637 is completely out of date, and that connection type was deprecated and removed in prior versions, so I'm not sure what version you're using for your benchmark. Use the [MultiplexedConnection](https://docs.rs/redis/latest/redis/aio/struct.MultiplexedConnection.html) without wrapping it with an `Arc` or a `Mutex` and you'll get a threadsafe multiplexed connection that doesn't need 21 threads for good perf :). You can follow [this example](https://github.com/redis-rs/redis-rs/blob/8796f0e15dd745c2f1f710f0f465e7bd86b2070e/redis/examples/async-multiplexed.rs#L33).

---

## Comment 4

> Username: nihohit  
> Created at: 2026-01-20 20:53:56 UTC  
> Url: https://github.com/boostorg/redis/issues/384#issuecomment-3774897402  

I'll be happy to help, if you get bogged down with how to use it for the benchmark :)

---

## Comment 5

> Username: mzimbres  
> Created at: 2026-01-20 22:07:47 UTC  
> Updated at: 2026-01-20 22:08:34 UTC  
> Url: https://github.com/boostorg/redis/issues/384#issuecomment-3775179528  

@nihohit The link referred to the old benchmarks, the new ones do use the multiplexed connection https://github.com/mzimbres/redis-cli-comp/blob/573b551656c9cd91f25b8df90fafcae1325ec46a/apps/redis-rs/src/main.rs#L41 and the latest redis-rs. You can review it and let me know if it can be improved. Thanks.  
  
.

---

## Comment 6

> Username: BijanVan  
> Created at: 2026-01-21 02:01:27 UTC  
> Updated at: 2026-01-21 02:01:51 UTC  
> Url: https://github.com/boostorg/redis/issues/384#issuecomment-3775772542  

Hi @mzimbres , I understand I was not running the same benchmark as you did, but having said that, Do you think mine are incorrect or missing something?

---

## Comment 7

> Username: nihohit  
> Created at: 2026-01-21 06:18:38 UTC  
> Url: https://github.com/boostorg/redis/issues/384#issuecomment-3776388058  

@mzimbres you're right, your code looks great :)

---

## Comment 8

> Username: nihohit  
> Created at: 2026-01-21 06:35:01 UTC  
> Updated at: 2026-01-21 06:35:15 UTC  
> Url: https://github.com/boostorg/redis/issues/384#issuecomment-3776427817  

Actually, there's one thing - `#[tokio::main(flavor = "current_thread")]` instead of `#[tokio::main]` will significantly reduce CPU usage, and in my local tests is also faster.  
  
BTW, if you're looking to expand your benchmarks, you can compare benchamrking methods and results with the [Valkey-Glide benchmarks](https://github.com/valkey-io/valkey-glide/tree/main/benchmarks). I'm sure that they'll be happy to hear if their client(s) or benchmarks can be improved.

---

## Comment 9

> Username: BijanVan  
> Created at: 2026-01-21 16:01:27 UTC  
> Url: https://github.com/boostorg/redis/issues/384#issuecomment-3779161118  

not sure why bench_mixed_pipeline in my benchmark is not representative of a real use-case as an example.   
At the core it puts a set of Redis command in the pipeline and then it executes them, I guess that is the whole idea about pipelining unless I am missing something.  
  
BTW, if you use multi-threaded tokio which is the default then the redis-rs would slower. For example  
for the Mixed Pipeline: redis-rs: 370,370 ops/sec boost::redis 389,277 ops/sec 0.95x. which is quite significant.

---

## Comment 10

> Username: nihohit  
> Created at: 2026-01-21 16:06:31 UTC  
> Url: https://github.com/boostorg/redis/issues/384#issuecomment-3779209223  

@BijanVan the core idea of async code is that multiple tasks can run concurrently. Your benchmark runs their tasks serially. Now, that's not "wrong", but it means that you're ignoring the reason for using async in the first case, and you're not saturating your CPU or network usage - you're either performing CPU work or waiting on the network, not both.

---

## Comment 11

> Username: BijanVan  
> Created at: 2026-01-21 17:57:03 UTC  
> Url: https://github.com/boostorg/redis/issues/384#issuecomment-3780152678  

So I changed bench_mixed_pipeline to something like below:  
```Rust  
async fn bench_mixed_pipeline(  
    conn: &MultiplexedConnection,  
    iterations: usize,  
    pipeline_size: usize,  
    read_ratio: f64,  
) -> BenchmarkStats {  
    let mut stats = BenchmarkStats::with_capacity(iterations);  
    let bench_start = Instant::now();  
    let mut remaining = iterations;  
  
    let mut futures = Vec::new();  
    let mut batch_sizes = Vec::new();  
    let mut start_times = Vec::new();  
  
    while remaining > 0 {  
        let batch = remaining.min(pipeline_size);  
        let mut pipe = redis::pipe();  
  
        {  
            let mut rng = rand::thread_rng();  
            for i in 0..batch {  
                if rng.r#gen::<f64>() < read_ratio {  
                    let key = format!("bench:key:{}", rng.gen_range(0..iterations));  
                    pipe.get::<String>(key);  
                } else {  
                    let key = format!("bench:key:{}", iterations - remaining + i);  
                    let value = generate_random_value(VALUE_SIZE);  
                    pipe.set::<String, String>(key, value);  
                }  
            }  
        }  
  
        let start = Instant::now();  
        start_times.push(start);  
        batch_sizes.push(batch);  
        let mut conn_clone = conn.clone();  
        futures.push(async move { pipe.query_async::<Vec<redis::Value>>(&mut conn_clone).await });  
  
        remaining -= batch;  
    }  
  
    let results = join_all(futures).await;  
  
    for (i, result) in results.into_iter().enumerate() {  
        let batch = batch_sizes[i];  
        let elapsed = start_times[i].elapsed();  
        match result {  
            Ok(_) => {  
                let per_cmd_ms = elapsed.as_secs_f64() * 1000.0 / batch as f64;  
                for _ in 0..batch {  
                    stats.latencies_ms.push(per_cmd_ms);  
                }  
            }  
            Err(_) => {  
                stats.errors += batch;  
            }  
        }  
    }  
  
    stats.total_duration = bench_start.elapsed();  
    stats  
}  
```  
  
and now throughput is: **496,074 ops/sec**  
[Rust code](https://godbolt.org/z/6KxrPYxK5)

---

## Comment 12

> Username: mzimbres  
> Created at: 2026-01-24 20:13:08 UTC  
> Url: https://github.com/boostorg/redis/issues/384#issuecomment-3795450742  

> Do you think mine are incorrect or missing something?  
  
It is totally fine but perhaps not representative of the majority of the scenarios where Redis is used.  It has only one task playing PING PONG with the server. Boost.Redis and other async clients however are designed to manage thousands of tasks  communicating concurrently with the server (this is what my benchmarks do). This covers better what internet facing HTTP/Websocket/etc servers do.  
  
In your case I think the pipeline benchmark is the important one  
  
- `BenchPing`, `BenchSet`, `BenchGet` send only one command at time. I find it unlikely that the event source would produce events at the exact same rate needed to execute commands. In general I expect commands to accumulate while are response is being waited for.  
  
Regarding the pipeline benchmarks  
  
- I would not use `SET` and `GET` because that only adds overhead on the server side to search for the key. You are however benchmarking clients not Redis itself.  
- I would not measure any time in the code, but use external tools such as `time` and `pidstat` to measure wall-clock, user/system time, CPU usage. Are your benchmarks saturating the CPU btw?  
- I don't understand how boost-redis performance could decay so much in the mixed pipeline case. From the client point of view PING, SET and GET are more or less the same, namely, they all return a string reply.  
  
I can include these measurement when I have more time.
