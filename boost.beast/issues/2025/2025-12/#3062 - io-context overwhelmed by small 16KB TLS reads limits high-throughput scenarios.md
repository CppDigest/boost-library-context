# #3062 - io-context overwhelmed by small 16KB TLS reads limits high-throughput scenarios [Open]

> Username: hofst  
> Created at: 2025-12-12 17:24:14 UTC  
> Updated at: 2026-02-06 16:40:04 UTC  
> Url: https://github.com/boostorg/beast/issues/3062  

We use boost beast to read from S3 in high-throughput scenarios: files with hundreds of GBs on machines with 192 cores and 100Gb/s network interfaces to S3 from ec2.  
The io-context is limited to ~500k operations per second and saturates at ~8 threads on our machines. More threads will just burn through CPU cycles fighting for locks.  
500k operations per second should be plenty to achieve 8+GB/s throughput but it isn't: Every read_some operation only reads a single TLS fragment (max size is 16KB) even when all buffers (socket and user-space buffers) are decently sized. This leads to a limit of 3GB/sec while still beeing inefficient with the io-context busy with tiny read operations.  
I tried a hacky boost modification to read more than one TLS fragment per read_some operation which promptly increased throughput by 3x and is close to the theoretical maximum of the machine. All while requiring fewer operations (and CPU utilization) in the io-context.  
  
I don't see any way to control this without modifying boost itself - am I missing something or is this scenario currently not covered?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-12-12 18:11:41 UTC  
> Url: https://github.com/boostorg/beast/issues/3062#issuecomment-3647601814  

which version of Boost are you using?

---

## Comment 2

> Username: hofst  
> Created at: 2025-12-12 18:29:00 UTC  
> Url: https://github.com/boostorg/beast/issues/3062#issuecomment-3647653058  

@vinniefalco boost 1.85 - but this has been troubling us for at least 3 years

---

## Comment 3

> Username: ashtum  
> Created at: 2025-12-12 20:01:17 UTC  
> Url: https://github.com/boostorg/beast/issues/3062#issuecomment-3647958062  

> 500k operations per second should be plenty to achieve 8+GB/s throughput but it isn't: Every read_some operation only reads a single TLS fragment (max size is 16KB) even when all buffers (socket and user-space buffers) are decently sized. This leads to a limit of 3GB/sec while still beeing inefficient with the io-context busy with tiny read operations.  
  
Using Asio's immediate executor should significantly reduce the scheduling cost, you can use [bind_immediate_executor](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/bind_immediate_executor.html) to bind an [inline_executor](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/inline_executor.html) to your handler so it just reads the available data and execute your handler immediately. To avoid stack exhaustion you should avoid binding a immediate executor every N operation (N could be like 16).  
  
Because your load is I/O heavy you might also try running one io_context per core to see if it makes a difference. With that you would be able to pass BOOST_ASIO_CONCURRENCY_HINT_UNSAFE_IO to each io_context and reduce contention.  
  
The tips described in this issue might be useful too: https://github.com/boostorg/beast/issues/2947

---

## Comment 4

> Username: vinniefalco  
> Created at: 2025-12-12 20:02:23 UTC  
> Url: https://github.com/boostorg/beast/issues/3062#issuecomment-3647961084  

I'd like to see your patch, do you have a fork of Asio you could share?

---

## Comment 5

> Username: hofst  
> Created at: 2025-12-13 10:46:53 UTC  
> Updated at: 2025-12-15 07:35:04 UTC  
> Url: https://github.com/boostorg/beast/issues/3062#issuecomment-3649206649  

@ashtum Thank you, I'll keep that in mind. But my instinct is that I rather remove the root cause (unnecessarily small tasks) than tweak the symptoms (make scheduling more efficient). I think the 500k operations we get from io-context should be plenty to solve the problem once the root cause is addressed (as my benchmarks show).  
  
@vinniefalco I'll check next week if I may share source code. In the meantime, the approach is as described above: read more in read_some in beast and change boost/asio/ssl to allow reading more. The tiny 16KB operations are the problem and once this is increased to, e.g., ~100KB problems simply disappear.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2025-12-13 20:25:40 UTC  
> Url: https://github.com/boostorg/beast/issues/3062#issuecomment-3649778371  

Where does beast do a 16kb read?

---

## Comment 7

> Username: hofst  
> Created at: 2025-12-14 10:36:00 UTC  
> Updated at: 2025-12-15 07:35:22 UTC  
> Url: https://github.com/boostorg/beast/issues/3062#issuecomment-3650685057  

@vinniefalco   
https://github.com/boostorg/beast/blob/67106ebaab7a644a9bdb5d6e6b0a3f4ad1d0e4f7/include/boost/beast/http/impl/read.hpp#L225  
  
The buffer is sized larger and is bounded by 64k in beast (it would be great to parametrize this upper limit).  
But this call `s_.async_read_some(*mb, std::move(self));` only reads a single TLS fragment for SSL streams which is 16KB. So every boost/beast op in the io-stream maps to 16KB at maximum.  
  
I don't want to get in a game of ping-pong where we blame either boost/beast or boost/asio/ssl, but:  
Either (1) there is a better way to use the streams in boost/beast to fill the buffers to larger sizes  
or (2) boost/asio/ssl needs to add a way to retrieve more bytes per `read_some` operation.

---

## Comment 8

> Username: hofst  
> Created at: 2026-01-28 12:55:40 UTC  
> Url: https://github.com/boostorg/beast/issues/3062#issuecomment-3811152884  

@vinniefalco   
Sorry for the delayed response - It took some time to get clearance to open-source the code and to clean it up.   
  
I have attached the patch that achieves the performance optimization discussed above, i.e., it reduces the number of required operations in the io-context by roughly an order of magnitude which in turn affects performance on a r8i.48xlarge as follows:  
- Bandwidth increases from ~25Gb/s to ~70Gb/s (on the 75GB/s network interface). Also measured ~90GB/s on a `x2idn.32xlarge` instance.  
- CPU utilization reduces from ~100% to ~10-20% depending on the number of threads in the io-context.  
  
After reducing the patch to the minimum required changes, it consists of the following parts:  
1. In boost/beast, it increases the maximum buffer read size from 64KB to 128KB (Could alternatively be controlled via a macro)  
2. In boost/asio/ssl, it (1) sets the BIO buffer to 128KB, it (2) increases the read granularity to the same, and most importantly, it (3) changes the  `read_op` such that it does not stop after a single TLS record but really utilizes the buffers to their optimal extent.  
  
I would appreciate feedback if there is a more elegant/idiomatic approach to achieve the same and how to best proceed.  
  
[patch_boost_improve_tls_efficiency.patch](https://github.com/user-attachments/files/24910911/patch_boost_improve_tls_efficiency.patch)

---

## Comment 9

> Username: vinniefalco  
> Created at: 2026-01-28 13:31:55 UTC  
> Url: https://github.com/boostorg/beast/issues/3062#issuecomment-3811329600  

could you please submit this as a pull request so it can be reviewed?

---

## Comment 10

> Username: hofst  
> Created at: 2026-02-06 15:20:14 UTC  
> Url: https://github.com/boostorg/beast/issues/3062#issuecomment-3861022742  

@vinniefalco sure, here you ago. As mentioned above, the only change in boost/beast is a slight tuning of the read-granularity, the meat of the change has been moved to asio/ssl:  
boost/beast PR: https://github.com/boostorg/beast/pull/3080  
asio/ssl PR: https://github.com/chriskohlhoff/asio/pull/1712

---

## Comment 11

> Username: ashtum  
> Created at: 2026-02-06 16:29:24 UTC  
> Url: https://github.com/boostorg/beast/issues/3062#issuecomment-3861350958  

> [@vinniefalco](https://github.com/vinniefalco) sure, here you ago. As mentioned above, the only change in boost/beast is a slight tuning of the read-granularity, the meat of the change has been moved to asio/ssl: boost/beast PR: [#3080](https://github.com/boostorg/beast/pull/3080) asio/ssl PR: [chriskohlhoff/asio#1712](https://github.com/chriskohlhoff/asio/pull/1712)  
  
How much difference did you measure when making the change in Asio only and not in Beast? Because the 64K buffer in Beast doesn’t seem as bad as the 16K limit in Asio.

---

## Comment 12

> Username: hofst  
> Created at: 2026-02-06 16:39:48 UTC  
> Updated at: 2026-02-06 16:40:04 UTC  
> Url: https://github.com/boostorg/beast/issues/3062#issuecomment-3861415484  

@ashtum The 128KB read granularity (across the whole stack including beast) further reduces cpu utilization by ~a third in my measurement compared to a 64KB granularity.
