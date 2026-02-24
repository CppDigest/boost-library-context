# #199 - how to await for a  shared resource from multiple coroutines [Closed]

> Username: mitsubishirgb  
> Created at: 2024-09-12 23:31:34 UTC  
> Updated at: 2024-10-21 16:51:26 UTC  
> Closed at: 2024-10-21 16:51:26 UTC  
> Url: https://github.com/boostorg/cobalt/issues/199  

multiple requests are being made on the same domain  and i want every coroutine to wait for the pending resolve  but in this snippet an error is thrown in the second pending co_await  
  
```cpp  
class DnsCache {  
public:  
    DnsCache() : resolver_(cobalt::this_thread::get_executor()) {}  
  
    cobalt::promise<std::optional<tcp::endpoint>> get_address(const std::string& domain) {  
        // Check if the domain is already cached  
        if (auto it = dns_cache.find(domain); it != dns_cache.end()) {  
            DEBUG_PRINT("[INFO] Address found in cache for domain: " + domain);  
            co_return it->second;  // Return cached address  
        }  
  
        // Check if there is a pending resolution  
        if (auto it = pending_resolutions.find(domain); it != pending_resolutions.end()) {  
            DEBUG_PRINT("[INFO] Waiting for pending resolution for domain: " + domain);  
            co_return co_await it->second;  // Await the result from the existing coroutine  
        }  
  
        // Create a new promise coroutine  
        auto resolution_coroutine = resolve_addr(domain, "443");  
  
        // Use emplace to insert the promise into the map  
        pending_resolutions.emplace(domain, std::move(resolution_coroutine));  
  
        // Await the resolution  
        auto resolved_addr = co_await resolution_coroutine;  
  
        if (resolved_addr.port() != 0) {  
            store_address(domain, resolved_addr);  // Cache the result if valid  
        }  
  
        // Remove the pending resolution  
        pending_resolutions.erase(domain);  
  
        co_return resolved_addr;  // Return the resolved address  
    }  
  
    void store_address(const std::string& domain, const tcp::endpoint& resolved_address) {  
        DEBUG_PRINT("[INFO] Storing resolved address for domain: " + domain);  
        dns_cache[domain] = resolved_address;  // Store the resolved address in the cache  
    }  
  
    cobalt::promise<tcp::endpoint> resolve_addr(const std::string& host, const std::string& port) {  
        system::error_code ec;  
        auto result = co_await resolver_.async_resolve(host, port, asio::redirect_error(use_op, ec));  
        if (ec) {  
            DEBUG_PRINT("Failed resolving " + host);  
            co_return{};  // Return an empty endpoint on error  
        }  
  
        if (result != tcp::resolver::iterator()) {  
            co_return result.begin()->endpoint();  // Return the first resolved endpoint  
        }  
  
        co_return{};  // Return an empty endpoint if no results were found  
    }  
  
private:  
    tcp::resolver resolver_;  // Resolver for asynchronous DNS lookups  
    std::unordered_map<std::string, tcp::endpoint> dns_cache;  // Cached DNS resolutions  
    std::unordered_map<std::string, cobalt::promise<tcp::endpoint>> pending_resolutions;  // Pending DNS resolutions  
};  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-09-15 23:30:16 UTC  
> Url: https://github.com/boostorg/cobalt/issues/199#issuecomment-2351832601  

What is the error? Are you sure you want to use const & in a coroutine argument list?  
  
Also: is this an exercise or a prod problem? Because the OS will cache DNS lookups for you.

---

## Comment 2

> Username: mitsubishirgb  
> Created at: 2024-10-09 23:00:35 UTC  
> Url: https://github.com/boostorg/cobalt/issues/199#issuecomment-2403571204  

> What is the error? Are you sure you want to use const & in a coroutine argument list?  
>   
> Also: is this an exercise or a prod problem? Because the OS will cache DNS lookups for you.  
  
i thought the resolver function will send a new dns query everytime, mb. Also the error was access violation it seems like the awaited resource is being moved from the coroutine so when other coroutines try to get the value it causes the exception, std::shared_ptr<T> didin't work for the promise is there any solution ?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-10-18 07:56:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/199#issuecomment-2421737761  

Not one built into cobalt. You can write your own awaitable type for that.
