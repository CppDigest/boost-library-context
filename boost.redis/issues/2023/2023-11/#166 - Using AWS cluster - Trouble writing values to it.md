# #166 - Using AWS cluster - Trouble writing values to it [Open]

> Username: ferchor2003  
> Created at: 2023-11-03 18:34:59 UTC  
> Updated at: 2025-04-15 08:42:01 UTC  
> Url: https://github.com/boostorg/redis/issues/166  

Hi,  
Working on my app, everything looks fine in my local development environment with a single redis server on standard port. Running into problems when using an AWS environment with a cluster. Is there anything specific that needs to be set for a cluster?  
  
The initial connection seems to go correctly using the following:  
```C++  
boost::redis::config cfg;  
cfg.addr.host = "clustercfg.xxx-xxx-xxx.cache.amazonaws.com";  
cfg.addr.port = "7000";  
cfg.use_ssl = true;  
cfg.clientname = "webserver";  
cfg.log_prefix = "webserver.redis ";  
```  
and I see the following on std::cout, so it seems the cluster was correctly identified:  
```  
webserver.redis Resolve results: 10.32.35.71:7000, 10.32.35.244:7000, 10.32.34.195:7000, 10.32.34.189:7000  
webserver.redis Connected to endpoint: 10.32.35.71:7000  
webserver.redis SSL handshake: Success  
webserver.redis Bytes written: 105  
webserver.redis Hello: Success  
webserver.redis Bytes written: 32  
  
```  
However, when trying an HSET operation I got an exception:  
 **Exception saving session to Redis. %s - keyid - Got RESP3 simple-error. [boost.redis:11]**  
  
This is the code that tries to write to the Cluster: (redisInst is an instance of sync_connection as it came from the examples dir)  
 ```C++  
    request req;  
	boost::redis::response<  
		int, // HSET  
		boost::redis::ignore_t// EXPIRE  
	> res;	  
	try {  
		req.push("HSET", key, "response", responseField);  
		req.push("EXPIRE", key, maxSessionTimeToLiveSecs, "NX");	// Have the session teid expire   
		redisInst->exec(req, res);  
		return true;  
	}  
	catch ( const std::exception& e )  
	{  
		LogError("Exception saving session to Redis. %s - %s", key.c_str(), e.what());  
	}  
```

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-11-03 19:53:55 UTC  
> Url: https://github.com/boostorg/redis/issues/166#issuecomment-1793016596  

> Running into problems when using an AWS environment with a cluster  
  
Do you mean a Redis cluster? So far there is not support for Redis-cluster in Boost.Redis.

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-11-03 19:56:26 UTC  
> Updated at: 2023-11-03 20:30:04 UTC  
> Url: https://github.com/boostorg/redis/issues/166#issuecomment-1793019234  

> Got RESP3 simple-error. [boost.redis:11]  
  
It is important to know what message is contained in that error.

---

## Comment 3

> Username: mzimbres  
> Created at: 2023-11-03 20:33:56 UTC  
> Url: https://github.com/boostorg/redis/issues/166#issuecomment-1793061750  

Can you please replace `response<int, boost::redis::ignore_t>` with `response<int, int>` and add this to your code  
  
```cpp  
      if (std::get<0>(resp).has_error()) {  
         std::cout << "Type: " << to_string(std::get<0>(resp).error().data_type) << std::endl;      
         std::cout << "Diagnostic: " << std::get<0>(resp).error().diagnostic << std::endl;          
      }  
      if (std::get<1>(resp).has_error()) {  
         std::cout << "Type: " << to_string(std::get<1>(resp).error().data_type) << std::endl;      
         std::cout << "Diagnostic: " << std::get<1>(resp).error().diagnostic << std::endl;          
      }  
```  
  
Let me then know what diagnostic do you get.

---

## Comment 4

> Username: ferchor2003  
> Created at: 2023-11-03 21:36:44 UTC  
> Url: https://github.com/boostorg/redis/issues/166#issuecomment-1793132790  

This is what the response now:  
```  
Type: simple_error  
Diagnostic: MOVED 10848 xxx-xxx-xxx-xxx-xxx.cache.amazonaws.com:7000  
Type: simple_error  
Diagnostic: MOVED 10848 xxx-xxx-xxx-xxx-xxx.cache.amazonaws.com:7000  
```  
Where xxx-xxx-xxx is one of the cluster nodes

---

## Comment 5

> Username: mzimbres  
> Created at: 2023-11-03 22:36:12 UTC  
> Url: https://github.com/boostorg/redis/issues/166#issuecomment-1793197751  

Ok, this means you are using a Redis cluster. As I said above, Boost.Redis does not provide any facility for cluster setups. In this case you would have to deal with the redirect yourself as describe under the in [MOVED Redirection](https://redis.io/docs/reference/cluster-spec/), which means creating a new connection to that node and maintaining a map of connections to prevent from opening more than one connection to the same node. Feel free to open an issue to add Redis cluster support, however I can't give you an estimate about when I will come up with it.

---

## Comment 6

> Username: nqf  
> Created at: 2025-04-15 03:24:46 UTC  
> Url: https://github.com/boostorg/redis/issues/166#issuecomment-2803667048  

Has there been any progress in supporting clusters?

---

## Comment 7

> Username: criatura2  
> Created at: 2025-04-15 08:42:00 UTC  
> Url: https://github.com/boostorg/redis/issues/166#issuecomment-2804285968  

@nql Unfortunately no. That would be a large task and I don't have the resources for implementing it at the moment.
