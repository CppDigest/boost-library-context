# #362 - Make flat_tree a proper container [Closed]

> Username: anarthal  
> Created at: 2025-12-01 10:56:59 UTC  
> Updated at: 2026-01-21 21:03:33 UTC  
> Closed at: 2026-01-18 21:08:19 UTC  
> Url: https://github.com/boostorg/redis/issues/362  

(From my conversation with @mzimbres on Slack) we currently have `tree` (aka `vector<node>`), which is a proper container. However, `flat_tree` isn't, creating inconsistencies:  
  
```cpp  
tree t1;  
flat_tree t2;  
for (const auto& elm: t1) { } // works  
for (const auto& elm: t2) { } // doesn't work  
```  
  
We can make `flat_tree` a proper container resembling `std::vector` interface. That'd mean adding the following members:  
  
* Typedefs iterator, const_iterator, value_type et all (see https://en.cppreference.com/w/cpp/container/vector.html).  
* begin, end (and maybe rbegin and rend?)  
* at, operator[], front, back, data  
* empty, size, capacity

---

## Comment 1

> Username: anarthal  
> Created at: 2025-12-01 10:57:31 UTC  
> Updated at: 2025-12-01 10:57:40 UTC  
> Url: https://github.com/boostorg/redis/issues/362#issuecomment-3595898910  

@D0zee since you've already worked on this before, would you like to take this issue?

---

## Comment 2

> Username: D0zee  
> Created at: 2025-12-04 14:19:54 UTC  
> Url: https://github.com/boostorg/redis/issues/362#issuecomment-3612492108  

Hi @anarthal, of course. I will take this issue

---

## Comment 3

> Username: mzimbres  
> Created at: 2026-01-03 20:55:00 UTC  
> Url: https://github.com/boostorg/redis/issues/362#issuecomment-3707350316  

The `flat_tree` API should provide access only to complete RESP3 messages, otherwise it is difficult to work with it in a loop as you showed above, therefore  
  
> at, operator[], front, back, data  
  
The API should not contain `back` and `data` because at the end there might be node of an incomplete message.  
  
> empty, size, capacity  
  
These functions should not take into consideration nodes at the end that are incomplete messages. Likewise the `begin` and `end` function should also take that into account. To do that we have to store the index and the last `notify_done()` call.  
  
We need this ideally before the next release, otherwise it will become a breaking change (removing `get_view()`).

---

## Comment 4

> Username: anarthal  
> Created at: 2026-01-03 22:15:25 UTC  
> Url: https://github.com/boostorg/redis/issues/362#issuecomment-3707405668  

I want to try an alternative approach here but I need to sit down and write (been working on my postgres library, I'll return here next week).  
  
The concern I've got with this aporoach is that I'm not sure of what clear() should do. IMO clear() should remove everything, including half-parsed nodes, but that breaks the approach. I feel that this container shouldn't be aware of these half-parsed messages, but that requires me to find an alternative.

---

## Comment 5

> Username: anarthal  
> Created at: 2026-01-04 10:39:34 UTC  
> Url: https://github.com/boostorg/redis/issues/362#issuecomment-3707959179  

I've considered buffering push nodes until a full message is read in an internal, intermediate container. Since we don't rotate the buffer until an entire message has been read, only the nodes (and not the strings) need to be kept. This is probably fast, but is still slower than the offset method you propose.  
  
Another alternative is adding a `consume_messages()` method that only removes full messages. So you'd use `clear()` (or assignment) when you need to discard everything (e.g. if you want to reuse the object in two unrelated `async_exec()` calls), and `consume_messages()` when using it in PubSub.  
  
Note that we need to store the string offset, too, so we know what things in the data buffer can be discarded.

---

## Comment 6

> Username: anarthal  
> Created at: 2026-01-04 10:40:57 UTC  
> Url: https://github.com/boostorg/redis/issues/362#issuecomment-3707960140  

@D0zee have you started working on this? I don't want to step on your toes. I can implement #369, with what Marcelo and me have been talking about, while keeping the old interface while you write the new one.

---

## Comment 7

> Username: mzimbres  
> Created at: 2026-01-04 17:10:12 UTC  
> Url: https://github.com/boostorg/redis/issues/362#issuecomment-3708258396  

The `flat_tree` is not a container in the STL sense so I don't really see a problem making `clear()` aware of incomplete messages at the end. It also exists to make it easier to work with pushes, `get_total_msgs()` is an example of that.  
  
I do recognize however there are some corner cases to address, for example, what happens when there are residual nodes on reconnection? In addition to `notify_done()` the adapter should perhaps also call `notify_init()` so that any residual nodes get discarded (and logged).  
  
> This is probably fast, but is still slower than the offset method you propose.  
  
To my surprise I found out in my benchmarks that using an offset was sligtly but consistently slower than rotating eagerly. This is very unintuitive because in the benchmark 4.3Gb/s are rotated. After some profiling it seems that by not rotating a lot of data gets flushed out of the cache and results in cache misses. I want to write something about this later.

---

## Comment 8

> Username: anarthal  
> Created at: 2026-01-05 20:11:09 UTC  
> Url: https://github.com/boostorg/redis/issues/362#issuecomment-3711931797  

The problem I was thinking of is with `async_exec`. This would cause problems:  
  
```cpp  
flat_tree t;  
co_await conn.async_exec(req, t);  
// Some error occurs and the response gets partially read. Now re-use the object for an unrelated operation  
  
t.clear(); // I'd expect this to completely wipe out state  
co_await conn.async_exec(req, t);  
```

---

## Comment 9

> Username: anarthal  
> Created at: 2026-01-05 20:15:43 UTC  
> Url: https://github.com/boostorg/redis/issues/362#issuecomment-3711944020  

>the adapter should perhaps also call notify_init() so that any residual nodes get discarded (and logged).  
  
I think this would actually address both issues transparently. We don't have a way to access the logger from the adapter, though.

---

## Comment 10

> Username: D0zee  
> Created at: 2026-01-21 18:36:33 UTC  
> Url: https://github.com/boostorg/redis/issues/362#issuecomment-3780488361  

@anarthal Sorry, I was really busy from the start of this year

---

## Comment 11

> Username: anarthal  
> Created at: 2026-01-21 21:03:32 UTC  
> Url: https://github.com/boostorg/redis/issues/362#issuecomment-3781152969  

No problem!
