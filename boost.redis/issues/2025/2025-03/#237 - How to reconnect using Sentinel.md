# #237 - How to reconnect using Sentinel ? [Closed]

> Username: hue-lee  
> Created at: 2025-03-25 02:10:01 UTC  
> Updated at: 2025-11-19 21:31:20 UTC  
> Closed at: 2025-11-19 21:31:20 UTC  
> Url: https://github.com/boostorg/redis/issues/237  

I'm trying to reconnect when the Redis server shutsdown using Redis Sentinel.   
When boost::redis::not_connected occurs while using async_exec, I want to connect to a valid Redis server using Sentinel.   
However, when async_exec uses config::reconnect_wait_interval, it keeps trying to connect to the previously disconnected Redis server.  
  
If I don't use config::reconnect_wait_interval, I can get a new IP from Sentinel after boost::redis::not_connected is returned by async_exec.   
But due to Redis failover time, I get the same IP/Port as before.   
If I use this to execute async_exec, "Connection lost: No connection could be made because the target machine actively refused it" occurs, and then async_exec hangs.  
  
Is there a way to connect to a valid Redis server using Sentinel?

---

## Comment 1

> Username: criatura2  
> Created at: 2025-04-09 08:42:24 UTC  
> Url: https://github.com/boostorg/redis/issues/237#issuecomment-2788816026  

Hi @hue-lee, I am back from vacation and will have a look at this soon.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-06-01 12:21:52 UTC  
> Url: https://github.com/boostorg/redis/issues/237#issuecomment-2927137044  

I've read some of [the Sentinel official docs](https://redis.io/docs/latest/operate/oss_and_stack/management/sentinel/) so get a feel of what this would imply. If I understood correctly, the idea is that Sentinels are processes that monitor Redis masters and serve as a configuration store for clients. So the algorithm would be something like:  
  
* The user configures the host and port of the Sentinel instance they want to connect to. Since Sentinel is distributed, it may make sense to allow passing several host/ports with the addresses of each Sentinel instance.  
* We connect to a Sentinel and issue a `SENTINEL get-master-addr-by-name <master-name>` to retrieve the details of the available masters. I don't know what's the best way of obtaining `<master-name>` here though - I think it can be retrieved from sentinel itself doing a `SENTINEL MASTERS` or allow the user to configure it.  
* Now establish a connection to the Redis master in the address/port that Sentinel told us.  
* When the connection fails, connect to Sentinel again and retrieve the details of any new master that got promoted.  
  
Am I understanding it correctly?

---

## Comment 3

> Username: mzimbres  
> Created at: 2025-06-01 15:07:40 UTC  
> Url: https://github.com/boostorg/redis/issues/237#issuecomment-2927392879  

> If I understood correctly, the idea is that Sentinels are processes that monitor Redis masters  
  
I think replicas are monitored as well otherwise the sentinel wouldn't know whether it is healthy to be promoted to a master on a failover. IIUC clients wishing to connect to replicas instead of master can do so by retrieving the replica address with  
  
```  
SENTINEL REPLICAS <master name> (>= 5.0) Show a list of replicas for this master, and their state.  
```  
  
> The user configures the host and port of the Sentinel instance they want to connect to. Since Sentinel is distributed, it may make sense to allow passing several host/ports with the addresses of each Sentinel instance.  
  
Good question. Sentinels discover other sentinels automatically so only one sentinel address is actually needed. Other instances can then be queried with  
  
```  
SENTINEL SENTINELS <master name> Show a list of sentinel instances for this master, and their state.  
```  
  
Moreover, sentinels can be added or removed on the fly so I guess if we pass a list of sentinels it would be only useful at startup, after that it would have to be ignored and only one of those addresses would be used anyway.  
  
> We connect to a Sentinel and issue a SENTINEL get-master-addr-by-name <master-name> to retrieve the details of the available masters. I don't know what's the best way of obtaining <master-name> here though  
  
A sentinel setup can monitor multiple master-replicas sets so I think the client must know which master/replica it wants to connect to.  
  
> - I think it can be retrieved from sentinel itself doing a SENTINEL MASTERS or allow the user to configure it.  Now establish a connection to the Redis master in the address/port that Sentinel told us.  When the connection fails, connect to Sentinel again and retrieve the details of any new master that got promoted.  Am I understanding it correctly?  
  
That is my understanding too. It is also possible to also listen to sentinel events to react more quickly, although I don't know much this brings. For example  
  
```  
+sentinel <instance details> -- A new sentinel for this master was detected and attached.  
```  
  
This can be used to update the internal list of available sentinels.  
  
```  
+sdown <instance details> -- The specified instance is now in Subjectively Down state.  
-sdown <instance details> -- The specified instance is no longer in Subjectively Down state.  
+odown <instance details> -- The specified instance is now in Objectively Down state.  
-odown <instance details> -- The specified instance is no longer in Objectively Down state.  
```  
  
Perhaps useful to speedup the failover when the health-checks interval is too high e.g. it is set to 10s and we get any event after 1s.

---

## Comment 4

> Username: mzimbres  
> Created at: 2025-06-09 21:27:37 UTC  
> Url: https://github.com/boostorg/redis/issues/237#issuecomment-2957077888  

[This](https://redis.io/docs/latest/develop/reference/sentinel-clients/) link describes in detail how to connect using a sentinel. Its step 3 consists of using a `ROLE` command to check whether the client is connected to a server with the correct role i.e. master or replica. In Boost.Redis however we won't have to do that because the response to the [HELLO](https://redis.io/docs/latest/commands/hello/) command already contains the role the server is playing.  
  
To use that information we can add a new [config](https://github.com/boostorg/redis/blob/a39d130240a9425cd610f9b6e6513377d775d8b1/include/boost/redis/config.hpp#L30) field, say `std::optional<bool> expected_master` that is checked [here](https://github.com/boostorg/redis/blob/a39d130240a9425cd610f9b6e6513377d775d8b1/include/boost/redis/detail/resp3_handshaker.hpp#L57). In case the response does not point to the correct whole the resp3 handshake should complete with an error which will result in `async_run` also completing with error. The sentinel resolve operation can then be repeated until the correct server is found.

---

## Comment 5

> Username: anarthal  
> Created at: 2025-10-23 17:25:33 UTC  
> Url: https://github.com/boostorg/redis/issues/237#issuecomment-3438230078  

I've been reading the docs, playing with Sentinels and servers, and I've written a quick-and-dirty implementation of this. I want to check with @mzimbres some design decisions I've made, before I go down a wrong path.  
  
1. Single `async_run`. We keep a single `async_run` overload (as opposed to `async_run_sentinel` or similar). I've added fields in `config` to distinguish regular run from Sentinel run.  
2. Config fields. This is what I've added (explanation follows)  
  
```cpp  
enum class server_role {  
    master,  
    replica,  
};  
  
struct sentinel_config {  
   std::vector<address> addresses{};  
   std::string master_name{};  
   bool use_ssl = false;  
   request setup{};  
   std::chrono::steady_clock::duration resolve_timeout = std::chrono::milliseconds{500};  
   std::chrono::steady_clock::duration connect_timeout = std::chrono::milliseconds{500};  
   std::chrono::steady_clock::duration ssl_handshake_timeout = std::chrono::milliseconds{500};  
   server_role role = server_role::master;  
};  
struct config {  
    // Other fields unmodified  
    sentinel_config sentinel;  
```  
  
* `cfg.sentinel.addresses` contains a list of hostname:ports where Sentinels listen. String IPs are also valid.  
* `cfg.sentinel.master_name` is the identifier you pass to `sentinel monitor` in Sentinel config. Used for `sentinel get-master-addr-by-name` and friends.  
* A run uses Sentinel if  `!sentinel.addresses.empty()`. Rationale: the empty string is a valid master name. Making `cfg.sentinel` a `std::optional<sentinel_config>` makes C++20 designated initializers less straightforward.  
* UNIX sockets are not supported at all with Sentinel. It makes no sense because they're local, and Sentinel isn't local, by definition. If `cfg.unix_socket` is used with Sentinel, an error is issued.  
* `cfg.addr` is completely ignored when using Sentinel.  
* `cfg.use_ssl` controls whether TLS is used when connecting to the Redis instances. `cfg.sentinel.use_ssl` controls whether TLS is used when connecting to Sentinels. Technically, you can use TLS with one and not with the other. I don't think anyone is (or should be) using this though, but it's possible. Do you have an opinion on whether this is worth it or not? Also, ignoring `cfg.addr` and not `cfg.use_ssl` might be counter-intuitive.  
* `cfg.sentinel.setup` is a setup request that is sent to Sentinel only, immediately before `sentinel get-master-addr-by-name` (actually, both are merged). The main point is allowing the user to set up Sentinel authentication. I prefer leaving this as a request so both ACL-based and old password authentication is supported. As well as users with needs we don't know of yet.  
* `cfg.setup`, `cfg.use_setup`, `cfg.username`, `cfg.password`, `cfg.clientname`, `cfg.database_index` are used to compose the setup request for the Redis instances, exactly like with regular connections. Note that authentication details in Sentinel and in Redis instance are typically different. For Sentinel, a `ROLE` command is added after all user-supplied requests. It must be last because `ROLE` may require authentication. As an optimization, we could detect if the setup request contains a `HELLO` and use the role returned there. But might not be worth it.  
* `cfg.sentinel.resolve_timeout`, `cfg.sentinel.connect_timeout`, `cfg.sentinel.ssl_handshake_timeout` are used when connecting to Sentinel, while `cfg.resolve_timeout`, `cfg.connect_timeout`, `cfg.ssl_handshake_timeout` are used when connecting to instances. Rationale: the Sentinel guide recommends using short timeout values for Sentinels, to quickly iterate over all the configured Sentinels. We could also ignore `cfg.resolve_timeout` and friends and use only the ones in `cfg.sentinel`. What do you think?  
* `cfg.sentinel.role` is used to perform the server role check. If set to `server_role::replica`, we will send a `sentinel replicas` instead of `sentinel get-master-addr-by-name`.  
* `cfg.health_check_id` and `cfg.health_check_interval` have the same meaning as with regular run.  
* `cfg.reconnect_wait_interval` is used to determine what to do when Sentinels can't be contacted. I actually have doubts about this. The guide recommends issuing an error if Sentinel can't be contacted or doesn't know about a master. But I don't think exiting `async_run` is going to be what our users expect. We mostly recommend using `asio::deferred` with `async_run`, so users won't detect that `async_run` exited. In my impl, if `cfg.reconnect_wait_interval != 0s`, and all Sentinels can't be contacted, or somehow errored when asking for the master (this includes "I don't know this master"), we sleep for the reconnection period and try again. If `cfg.reconnect_wait_interval == 0s`, we don't, and `async_run` completes. What do you think?  
* `cfg.max_read_size` and `cfg.read_buffer_append_size` keep their meaning.  
  
By writing this, I feel it's pretty complex. Any ideas on how to trim complexity are welcome.  
  
When issuing `sentinel get-master-addr-by-name` (or its replica equivalent), we also issue a `sentinel sentinels` command and update our internal Sentinel list. We never remove Sentinels, only add new ones. Do you think this could be a problem?  
  
Let me know what you think.

---

## Comment 6

> Username: mzimbres  
> Created at: 2025-10-23 21:08:24 UTC  
> Url: https://github.com/boostorg/redis/issues/237#issuecomment-3439197234  

Wow, thanks. I will read more carefully on the weekend. I will shortly describe here my mental model here too for appreciation so we can process each other ideas in parallel  
  
Originally, I saw the sentinel support as an extension to the resolve operation, so instead of calling `ip::resolver::async_resolve` we would cal `sentinel_resolver::async_resolve(config)` where the config object carries a list of addresses and the role that is wanted. We would then wrap `ip::resolver` and `sentinel_resolver` into a `redis::resolver `and use it in the `redis_stream` everything else would be kept as is.  
  
However, I have been reading some Redis issues recently and it looks like a good client should subscribe to sentinel events to speed up failover. That does not fit in the model I describe above and would be too complex to embed in our connection i.e. would bloat it.  
  
Given our `redis::connection` is in very good state i.e. tests, cancellation support etc. I think it should be used as a primitive to resolve the sentinel. We would add a new IO object e.g. `redis::sentinel` that would use `redis::connection` internally to talk to the sentinel, resolve addresses and subcribe to events. For example  
  
```cpp  
connection conn{ex}  
sentinel st{ex};  
  
co_await st.async_adopt(conn);  
```  
  
The `async_adopt` operation would resolve the master/replica addresses ( similar to [this](https://github.com/boostorg/redis/blob/develop/example/cpp20_resolve_with_sentinel.cpp#L32-L58)), create a `config` with that address and call `conn.async_run`. It would also subscribe to sentinel events, cancel `async_run` when necessary and call it with the new address.

---

## Comment 7

> Username: anarthal  
> Created at: 2025-10-24 16:17:38 UTC  
> Url: https://github.com/boostorg/redis/issues/237#issuecomment-3443952078  

The model I've described above is more in the line of your `sentinel_resolver` than in the second approach you're describing. As you said, it doesn't support well sdown PubSub events. I was hoping that our health checks were enough to detect failovers and this would not be needed.  
  
I'm not very keen on having another I/O object and extra async operations because:  
  
* The user needs to decide at compile-time whether they want Sentinel support, rather than at runtime.  
* The API is much wider and cumbersome, because of Asio specifics. We need constructors from executors and execution contexts, proper cancellation support, tests for `cancel_after`, and so on.  
  
That said, maybe we can blend the two models, refactor the current `async_run` and `connection_impl` into a `detail::simple_connection`, and use this as a base. I need to give this a think.

---

## Comment 8

> Username: mzimbres  
> Created at: 2025-10-24 21:08:43 UTC  
> Url: https://github.com/boostorg/redis/issues/237#issuecomment-3444917643  

> I was hoping that our health checks were enough to detect failovers and this would not be needed.  
  
I thought the same. The sentinel won't do anything superior but also send pings to check the health of its instances, therefore it can't actually detect failed instances faster than our own health checks. Perhaps their recommendation does not apply to our client and we can take the easier route. If for whatever reason a user needs sentinel events he can use the connection to subscribe to them directly.

---

## Comment 9

> Username: anarthal  
> Created at: 2025-10-24 22:40:55 UTC  
> Url: https://github.com/boostorg/redis/issues/237#issuecomment-3445133475  

Should we start with the easy route for now then?

---

## Comment 10

> Username: mzimbres  
> Created at: 2025-10-25 12:04:49 UTC  
> Url: https://github.com/boostorg/redis/issues/237#issuecomment-3446620206  

> Should we start with the easy route for now then?  
  
Yes, go ahead. There must be something missing from that recommendation about subscribing to sentinel events. If we start to rely on sentinel events we will also have to health-check it. It does not make sense to me.
