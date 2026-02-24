# #19 Resolve canonical_name to support "localhost" on windows [Closed]

> Username: Faaux  
> Created at: 2024-10-21 14:01:50 UTC  
> Updated at: 2024-12-17 11:31:03 UTC  
> Closed at: 2024-12-17 11:30:56 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/19  

Hi Mireo Team,  
  
on Windows async_mqtt5 fails to resolve `localhost`. Changing to a canonical resolver fixes this.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-10-23 07:20:32 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/19#issuecomment-2431124918  

## Welcome to [Codecov](https://codecov.io?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=mireo) :tada:  
  
Once you merge this PR into your default branch, you're all set! Codecov will compare coverage reports and display results in all future pull requests.  
  
Thanks for integrating Codecov - We've got you covered :open_umbrella:

---

## Comment 2

> Username: ksimicevic  
> Created_at: 2024-10-23 07:58:55 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/19#issuecomment-2431209770  

Hello and thank you very much for submitting this PR!  
We are currently undergoing a Boost review until the end of this week. We will look at this PR next week.  
Sorry for the inconvenience.

---

## Comment 3

> Username: Faaux  
> Created_at: 2024-10-31 10:18:07 UTC  
> Updated_at: 2024-10-31 10:18:25 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/19#issuecomment-2449507183  

First of all congrats on passing with condition :)  
  
I just found another case in Docker + WSL2 where localhost (with the fix applied) couldn't be resolved. So something is still amiss  but I am out of my ASIO depth here.

---

## Comment 4

> Username: ksimicevic  
> Created_at: 2024-11-04 11:46:57 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/19#issuecomment-2454502224  

Sorry for taking so long to get back to you.  
  
I tried to replicate the issue you described on Windows 11 by installing a mosquito broker and running it locally. I compiled my client code with MSVC 14.37.   
  
I could not connect to my local broker. However, I did not find any issues with the resolver. It resolved `localhost` successfully and returned 2 endpoints (IPv6 and IPv4). In my case, the issue was in [connect_op.hpp:112](https://github.com/mireo/async-mqtt5/blob/0b935bd1a2ba65427d1fb491aa1133b07c076cd6/include/async_mqtt5/impl/connect_op.hpp#L112) where we call `async_connect` with the first endpoint in the list (which was the IPv6 endpoint here and my broker was listening on IPv4).  I fixed the issue by changing the `perform` function to use the free function `boost::asio::async_connect` that will iteratively try to connect to every endpoint in the list until it successfully establishes a connection:  
```  
void perform(  
	const epoints& eps, authority_path ap  
) {  
	auto& stream = lowest_layer(_stream);  
	asio::async_connect(  
		stream, eps,  
		asio::append(  
			asio::prepend(std::move(*this), on_connect {}),  
			std::move(ap)  
		)  
	);  
}  
```  
Additionally, I tried running both the broker and the client code on WSL2 (Debian 12 distro) and encountered no issues.  
  
Therefore, I think I need more information about your situation to figure out what is going on:  
1. When you said that your client failed to resolve to `localhost` on Windows and Docker+WSL2, does that mean you have received an error_code in `resolve_op` in [endpoints.hpp:109](https://github.com/mireo/async-mqtt5/blob/master/include/async_mqtt5/impl/endpoints.hpp#L109)? If yes, what was the `resolve_ec` and `timer_ec`?  
2. Could you precisely specify the client and broker setups where you encountered the issue (the Windows version, WSL2 distribution, and compilers used to compile your client code)?  
  
Thanks!

---

## Comment 5

> Username: Faaux  
> Created_at: 2024-11-08 08:07:54 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/19#issuecomment-2464026429  

You are probably right. This would also explaing the Docker side of things within the setup I have here.  
  
I will try and check if the change to `perform` fixes this for us and then close this PR.

---

## Comment 6

> Username: Faaux  
> Created_at: 2024-11-08 10:00:05 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/19#issuecomment-2464287307  

So I just tested on Windows (can't in Docker at the moment).  
  
**Versions**  
- Windows: Version	10.0.22621 Build 22621  
- Visual Studio 2022 17.10.3  
- `cl --version` -> `Microsoft (R) C/C++ Optimizing Compiler Version 19.40.33811 for x64`  
- mosquitto version 2.0.15 (default install)  
- Boost 1.84.0  
  
**Mosquitto Output on Startup**  
<details>  
1731059274: mosquitto version 2.0.15 starting  
1731059274: Using default config.  
1731059274: Starting in local only mode. Connections will only be possible from clients running on this machine.  
1731059274: Create a configuration file which defines a listener to allow remote access.  
1731059274: For more details see https://mosquitto.org/documentation/authentication-methods/  
1731059274: Opening ipv4 listen socket on port 1883.  
1731059274: Opening ipv6 listen socket on port 1883.  
</details>  
  
**Client Code**  
```c++  
boost::asio::io_context io;  
  
auto client = std::make_shared<async_mqtt5::mqtt_client<boost::asio::ip::tcp::socket>>(io);  
auto clientId = "...";  
  
client->brokers("localhost").keep_alive(5).credentials(clientId);  
  
boost::asio::post(io, [client]() {  
  client->async_run(boost::asio::detached);  
});  
  
std::vector<async_mqtt5::subscribe_topic> topics;  
topics.push_back(async_mqtt5::subscribe_topic{  
  "test",  
  async_mqtt5::subscribe_options{async_mqtt5::qos_e::exactly_once,  
                                 async_mqtt5::no_local_e::yes,  
                                 async_mqtt5::retain_as_published_e::retain,  
                                 async_mqtt5::retain_handling_e::send}});  
  
boost::asio::post(io, [client, topics = std::move(topics)]() {  
  client->async_subscribe(topics,  
                          async_mqtt5::subscribe_props{},  
                          [](async_mqtt5::error_code,  
                             std::vector<async_mqtt5::reason_code>,  
                             async_mqtt5::suback_props) {  
                            // This is never reached  
                          });  
});  
  
auto runForeverThread = std::thread([io = &io]() {  
  io->run();  
});  
runForeverThread.join();  
```  
  
**Original Version**  
Connecting fails in `connect_op.hpp` `void operator()(on_connect, error_code ec, endpoint ep, authority_path ap)` with `system:1214` (`ERROR_INVALID_NETNAME`)  
  
**With only PR Changes**   
Connecting fails in `connect_op.hpp` `void operator()(on_connect, error_code ec, endpoint ep, authority_path ap)` with `system:1214` (`ERROR_INVALID_NETNAME`)  
  
(Sorry I must have done some testing wrong when I first proposed this PR :( )  
  
**With only changes to `perform`**  
Connecting fails in `connect_op.hpp` `void operator()(on_fixed_header, error_code ec, size_t num_read)` with `misc:2`  
(dont know if there is an earlier failure that I missed)  
  
**With PR and `perform` changes**  
Connecting fails in `connect_op.hpp` `void operator()(on_fixed_header, error_code ec, size_t num_read)` with `misc:2`  
  
Swapping from `"localhost"` to `"127.0.0.1"` connects in all mentioned variations.

---

## Comment 7

> Username: ksimicevic  
> Created_at: 2024-11-08 12:08:39 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/19#issuecomment-2464600620  

Thank you for the very detailed report!  
Don't worry about the PR, you helped us discover a bug in our code :)  
  
I noticed that the broker running in default mode listens on both IPv6 and IPv4.  Additionally, when I run your code (or any other example that connects to `localhost`) with the changes in the `perform` function, I always establish an IPv6 connection. Considering that internally we open the `asio::ip::tcp::socket` with `tcp::v4()`, this should not work at all. I am surprised it works on my machine.  
  
The fact that your code works when you swap to `127.0.0.1` makes me believe the issue might be in the IPv6 connection.  
To confirm that, could you please try running your mosquito broker with a config that disables listening on IPv6?  
  
Your config file should look like this:  
```  
listener 1883 localhost  
socket_domain ipv4  
```  
Run your mosquito broker with:  
```  
mosquitto -c "<path to config file>" -v  
```  
  
Please try your code with `localhost` and changes in the `perform` function again and let me know the results.

---

## Comment 8

> Username: Faaux  
> Created_at: 2024-11-08 13:15:36 UTC  
> Updated_at: 2024-11-08 13:16:45 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/19#issuecomment-2464744701  

First of all I tracked down one issue in the work firewall that might explain why it didn't connect at all before and I got `misc:2 eof`.  
  
Then I did some tests (again Windows only at the moment)  
  
**With `perform` change**  
Can connect to mosquitto running in `ipv4` and `ipv6` as well as running with both. 🥳   
  
**Without `perform` change**  
Can't connect at all: `system:1214` (ERROR_INVALID_NETNAME)  
  
Hope this helps

---

## Comment 9

> Username: ksimicevic  
> Created_at: 2024-11-08 14:41:33 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/19#issuecomment-2464929799  

Good job on figuring it out! Hopefully, that fixes the issue on WSL2 as well.

---

## Comment 10

> Username: ksimicevic  
> Created_at: 2024-12-17 11:30:56 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/19#issuecomment-2548213882  

Resolved with f80c1897675f65174788e96f907775b8fb33c0b5.

---
