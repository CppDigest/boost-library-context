# #43 - Better support for working with XREADGROUP and XREAD replies [Open]

> Username: nejati-deriv  
> Created at: 2023-01-18 12:32:07 UTC  
> Updated at: 2023-09-02 18:33:47 UTC  
> Url: https://github.com/boostorg/redis/issues/43  

`XREADGROUP` and `XREAD` replies have the same structure. if we use `std::vector< aedis::resp3::node< std::string > >` for their response it will be very difficult to work with their replies.  
It would be great if you could add some library support to make working with Redis streams easier.  
```BASH  
> XREAD COUNT 2 STREAMS stream_1 stream_2 0  
1) 1) "stream_1"  
   2) 1) 1) 1519073278252-0  
         2) 1) "key_1"  
            2) "value_1"  
            3) "key_2"  
            4) "value_2"  
      2) 1) 1519073279157-0  
         2) 1) "key_1"  
            2) "value_1"  
            3) "key_2"  
            4) "value_2"  
      3) 1) 1519073279157-0  
         2) 1) "key_1"  
            2) "value_1"  
            3) "key_2"  
            4) "value_2"  
2) 1) "stream_2"  
   2) 1) 1) 1519073278252-0  
         2) 1) "key_1"  
            2) "value_1"  
      2) 1) 1519073279157-0  
         2) 1) "key_1"  
            2) "value_1"  
```

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-08-31 11:02:32 UTC  
> Url: https://github.com/boostorg/redis/issues/43#issuecomment-1700829174  

@anarthal You used Boost.Redis and streams in the boost-servertech, if you have any good idea on how to implement this case feel free to chime in. Thanks.

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-08-31 11:03:34 UTC  
> Url: https://github.com/boostorg/redis/issues/43#issuecomment-1700830437  

@ashtum Are you still using the account you used to open this ticket?

---

## Comment 3

> Username: ashtum  
> Created at: 2023-08-31 11:18:23 UTC  
> Url: https://github.com/boostorg/redis/issues/43#issuecomment-1700850016  

No, I don't have access to that.

---

## Comment 4

> Username: anarthal  
> Created at: 2023-08-31 13:38:31 UTC  
> Url: https://github.com/boostorg/redis/issues/43#issuecomment-1701058366  

So something I've seen is that it's difficult to escape from `generic_response` in the general case. It may be my lack of knowledge about Boost.Redis. For example, there is a point where I pipeline several `XADD` into a single request. The number of commands is only known at runtime. The response to each `XADD` is a string. Using `vector<string>` doesn't work because that's suitable for a single command returning a list of strings, not several responses with a single string.  
  
What I'm trying to say is that I'd go down the path of making `generic_response` easier to work with. That would cover you this issue until we get more field experience, as well as more exotic responses we don't know of yet.  
  
What I find difficult about `generic_response` is verifying that the data structure matches the shape I'm expecting. I think it's a good internal representation, but it's too low-level for the general public. You can find how I'm using it [here](https://github.com/anarthal/servertech-chat/blob/master/server/src/redis_serialization.cpp) (please do suggest improvements if you see they can be done).  
  
So I'd suggest an alternative, tree-like structure, that offers you methods to retrieve each node's children, at least. Something like Boost.Json `value`.  
  
Other than that, each entry returned by `XRANGE` and `XREVRANGE` is something like  
```  
struct stream_entry  
{  
    string id;  
    map<string, any> attributes;  
};  
```  
  
I've only seen attribute strings, idk if they can be of other types.  
  
But then the result of `XREAD` is more complex because it includes the stream ID, too.  
  
I suggest you to have a look at the TypeScript driver for node.js, since IIRC they include detailed type definitions for every command result - you can get some inspiration from this.

---

## Comment 5

> Username: mzimbres  
> Created at: 2023-09-02 13:27:01 UTC  
> Url: https://github.com/boostorg/redis/issues/43#issuecomment-1703833771  

Yes, it is too low level for the general public. We need two things  
  
1. A way of iterating over the `generic_response` individual responses.  For example, if we receive the responses to a request that contains multiple commands in a `generic_response` we don't know where the first response finishes and the next one starts. Each message is contained in a range of `resp3::node`s. This would also be useful to support reading server pushes in batches to improve performance.  
  
2. Once we have a range in the `generic_response` that corresponds to an individual message we will want to convert that into a more usable data structure, it could be like this  
  
```cpp  
generic_response gresp;  
...  
error_code ec;  
auto map = as<std::map<user, value>>(gresp-range, ec)  
```  
  
  
> So I'd suggest an alternative, tree-like structure, that offers you methods to retrieve each node's children, at least. Something like Boost.Json value.  
  
A tree would be the most generic thing, but too generic and therefore not very efficient for most cases. I believe most requests don't have a dynamic number of commands.

---

## Comment 6

> Username: anarthal  
> Created at: 2023-09-02 15:01:41 UTC  
> Url: https://github.com/boostorg/redis/issues/43#issuecomment-1703857720  

> A way of iterating over the generic_response individual responses  
  
Can't you just use `basic_node::depth` to distinguish responses? Every time you encounter a node with `depth == 0`, that's a new response. That's what I'm doing in servertech. Why is it different to parsing other data structures?  
  
> A tree would be the most generic thing, but too generic and therefore not very efficient for most cases.  
  
I'm proposing an additional, opt-in mechanism, not replacing `generic_response`.

---

## Comment 7

> Username: mzimbres  
> Created at: 2023-09-02 18:33:47 UTC  
> Url: https://github.com/boostorg/redis/issues/43#issuecomment-1703908006  

> Can't you just use basic_node::depth to distinguish responses? Every  
> time you encounter a node with depth == 0, that's a new response.  
> That's what I'm doing in servertech. Why is it different to parsing  
> other data structures?  
  
Yes, that works too. I should indeed add this to the docs. There just should be a more elegant way of iterating over a generic_response than checking depths manually.
