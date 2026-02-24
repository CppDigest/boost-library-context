# #278 Generic flat response implementation [Closed]

> Username: D0zee  
> Created at: 2025-06-27 19:38:13 UTC  
> Updated at: 2025-11-18 13:16:24 UTC  
> Closed at: 2025-11-18 13:16:24 UTC  
> Url: https://github.com/boostorg/redis/pull/278  



---

## Review 1 [Commented]

> Username: anarthal  
> Created_at: 2025-06-27 20:10:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/278#pullrequestreview-2967823232  

I've reviewed the flat_response_impl part. It looks what I'd expect. Marcelo is more suited to review the rest, since I don't really know how adapters work. Thanks for submitting the patch.

📁 include/boost/redis/response.hpp

```diff
  87 |+       new_node.size = nd.value.size();
  88 |+ 
  89 |+       data_ += std::string{std::cbegin(nd.value), std::cend(nd.value)};
```

> Username: anarthal  
> Created_at: 2025-06-27 19:57:42 UTC  
> Updated_at: 2025-06-27 20:10:28 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2172783303  

This is making an avoidable copy - I'd suggest .append()

> Username: D0zee  
> Created_at: 2025-06-27 23:10:07 UTC  
> Updated_at: 2025-06-27 23:24:28 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2172965583  

Agreed!

---

📁 include/boost/redis/response.hpp

```diff
  95 |+    resp3::node_view make_node_view(const resp3::offset_node& n)
  96 |+    {
  97 |+       return resp3::node_view{
```

> Username: anarthal  
> Created_at: 2025-06-27 19:59:38 UTC  
> Updated_at: 2025-06-27 20:10:28 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2172788825  

Named initializers are C++20

---

📁 include/boost/redis/response.hpp

```diff
  43 |+       using pointer = void;
  44 |+       using reference = value_type;
  45 |+       using iterator_category = std::forward_iterator_tag;
```

> Username: anarthal  
> Created_at: 2025-06-27 20:00:36 UTC  
> Updated_at: 2025-06-27 20:10:28 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2172790007  

I think we can make this random access (although it will mean implementing more iterator methods)

---

📁 include/boost/redis/response.hpp

```diff
  89 |+       data_ += std::string{std::cbegin(nd.value), std::cend(nd.value)};
  90 |+ 
  91 |+       view_.push_back(std::move(new_node));
```

> Username: anarthal  
> Created_at: 2025-06-27 20:04:06 UTC  
> Updated_at: 2025-06-27 20:10:28 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2172796444  

Does this std::move add anything?

> Username: D0zee  
> Created_at: 2025-06-27 23:22:12 UTC  
> Updated_at: 2025-06-28 04:23:04 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2172972001  

Frankly there is no difference between moving and copying in this case as the `offset_node` is trivially copyable. However I would prefer to move it anyway

---

📁 include/boost/redis/response.hpp

```diff
  35 | using generic_response = adapter::result<std::vector<resp3::node>>;
  36 | 
  37 |+ struct flat_response_impl {
```

> Username: anarthal  
> Created_at: 2025-06-27 20:08:55 UTC  
> Updated_at: 2025-06-27 20:10:28 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2172803124  

I'd avoid the _impl suffix for public types - maybe flat_response_value?

> Username: D0zee  
> Created_at: 2025-06-27 23:08:00 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2172964678  

I'm thinking about putting this structure into `boost::redis::response::detail`namespace as well

> Username: anarthal  
> Created_at: 2025-06-29 17:05:14 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2173831544  

I'd keep it public. `adapter::result` is just a `system::result` type, so the user needs to interact with `flat_response_impl` and know its API.


📁 include/boost/redis/resp3/node.hpp

```diff
  70 |+  * TODO: documentation
  71 |+  */
  72 |+ struct offset_node : node_view {
```

> Username: anarthal  
> Created_at: 2025-06-27 20:07:21 UTC  
> Updated_at: 2025-06-27 20:10:28 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2172801192  

Do we need to expose this as public? Looks like an implementation detail


---

## Comment 2

> Username: D0zee  
> Created_at: 2025-06-27 22:59:13 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3014581583  

@anarthal It's not really ready, just wanted to make sure that I'm on right direction. Thank you for your review!

---

## Comment 3

> Username: mzimbres  
> Created_at: 2025-06-29 21:04:35 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3017121159  

Hi @D0zee, I've looked at this PR again and think it has a good direction so I endorse further development. The two issues I talked about in the issue are minor and can be addressed in the future if at all, i.e.  
  
1. `offset_node` vs `offset_string`.  
2. `node_view at(std::size_t)` vs `node_view const& at(std::size_t)`  
  
Thanks again for the time invested.

---

## Comment 4

> Username: D0zee  
> Created_at: 2025-06-30 05:30:47 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3017855271  

Hi @mzimbres, I'm going to look at suggested issues in details and implement them to enhance performance and make the structure more user-friendly. Thank you for your comments in the issue

---

## Review 5 [Commented]

> Username: mzimbres  
> Created_at: 2025-07-02 21:06:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/278#pullrequestreview-2980631006  

📁 include/boost/redis/response.hpp

```diff
  54 |+       offset_string.size = nd.value.size();
  55 |+ 
  56 |+       data_.append(nd.value.data());
```

> Username: mzimbres  
> Created_at: 2025-07-02 21:05:59 UTC  
> Updated_at: 2025-07-02 21:06:00 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2180971322  

`nd.value.data()` does not point to null terminated strings in general so you have to pass the size as well.


---

## Review 6 [Commented]

> Username: mzimbres  
> Created_at: 2025-07-02 21:19:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/278#pullrequestreview-2980663620  

📁 include/boost/redis/response.hpp

```diff
  57 |+ 
  58 |+       offset_string.data = std::string_view{
  59 |+          data_.data() + offset_string.offset,
```

> Username: mzimbres  
> Created_at: 2025-07-02 21:19:28 UTC  
> Updated_at: 2025-07-02 21:19:29 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2180990749  

If the next call to `data_.append` from the previous line causes the string to reallocate, this pointer (`data_.data()`) will become invalid. To address this I have proposed in other comments to use the `notify_done` callback to traverse the flat response initializing the `string_view` of all offset strings. I have also considered delegating this step to the user, for example  
```cpp  
generic_flat_response resp;  
co_await conn.async_exec(req, resp);  
resp.set_views(); // Traverses all nodes setting the strings.  
```  
I am still not sure though as this is error prone.

> Username: D0zee  
> Created_at: 2025-07-03 04:37:43 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2181673218  

Oh, you are correct. Will fix it

> Username: D0zee  
> Created_at: 2025-07-05 18:41:17 UTC  
> Updated_at: 2025-07-05 18:41:18 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2187578247  

This comment was sorted out. Tested on `cpp20_subscriber`, it works as expected. If you have any other comments about `prepare_done` function or anything else, please let me know


---

## Review 7 [Commented]

> Username: mzimbres  
> Created_at: 2025-07-05 20:06:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/278#pullrequestreview-2990575757  

> Username: mzimbres  
> Created_at: 2025-07-05 20:06:08 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2187657373  

I think we can just replace generic_response with generic_flat_response in all occurrences.

> Username: D0zee  
> Created_at: 2025-07-05 20:25:06 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2187671769  

It will be good test for API of new class because I already see some absent methods like `clear()`.


---

## Review 8 [Commented]

> Username: mzimbres  
> Created_at: 2025-07-05 20:19:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/278#pullrequestreview-2990585490  

📁 include/boost/redis/adapter/detail/adapters.hpp

```diff
 143 |+ 
 144 |+ template <typename T>
 145 |+ auto prepare_done(T&) noexcept -> done_fn_type
```

> Username: mzimbres  
> Created_at: 2025-07-05 20:19:29 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2187667555  

I don't think there any need to type erase the return value at this point. We should delay it to the calling point where `prepare_done` is actually called.


---

## Review 9 [Commented]

> Username: mzimbres  
> Created_at: 2025-07-05 20:24:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/278#pullrequestreview-2990589134  

📁 include/boost/redis/response.hpp

```diff
  80 |- void consume_one(generic_response& r);
 135 |+ template <typename Response>
 136 |+ void consume_one(Response& r)
```

> Username: mzimbres  
> Created_at: 2025-07-05 20:24:25 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2187671178  

Can we avoid turning this function into a template? We can have two overloads, one for `generic_response` and one for `generic_flag_response` and move their implementations to the `.ipp` file. We want to avoid any increase in compilation times.

> Username: D0zee  
> Created_at: 2025-07-05 20:27:19 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2187674564  

The difference between two specializations is actually one string, that's why I made it template. However if I want to avoid any increase in compilation time, then you are right, I will fix it


---

## Review 10 [Commented]

> Username: mzimbres  
> Created_at: 2025-07-05 20:42:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/278#pullrequestreview-2990618167  

📁 include/boost/redis/adapter/any_adapter.hpp

```diff
  40 |-       fn_type adapt_fn;
  42 |+       adapt_fn_type adapt_fn;
  43 |+       done_fn_type prepare_done_fn;
```

> Username: mzimbres  
> Created_at: 2025-07-05 20:42:13 UTC  
> Updated_at: 2025-07-05 20:42:14 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2187696560  

I would like to avoid the creation of two `std::function` for each request (i.e. `async_exec` calls) since each one represents a potential dynamic allocation. In this case however I think we can't do much better because inside `async_exec` we only have access to the type erased adapter i.e. `std::function` and therefore we can't call `adapter.set_done()` for example. I think the `prepare_done_fn` callback is not critical though since it is small and the dynamic allocation is likely to be optimized away with SOO (small object optimization). @anarthal What do you think?

> Username: D0zee  
> Created_at: 2025-07-06 07:30:31 UTC  
> Updated_at: 2025-07-06 07:33:45 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2188081444  

```  
sizeof(generic_flat_response*)=8  
sizeof(generic_flat_response&)=64  
sizeof([](){})=1  
```  
  
- If response type is `generic_flat_response` we will return lambda with captured reference (`[res]()mutable{}`). The size of this lambda is 64 bytes, `std::function` will definitely keep it on heap. I suggest to capture and pass the pointer`generic_flat_response*`. Its size is 8 bytes and in this case SOO will take place during creation of `std::function` in `any_adapter`. As a result we have no heap allocations in this case.  
  
- If the response type is any other SOO will take a place by default due to the fact that size of empty lambda is 1 byte.  
  
Source: https://stackoverflow.com/a/57049013

> Username: mzimbres  
> Created_at: 2025-07-06 16:28:52 UTC  
> Updated_at: 2025-07-06 16:34:04 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2188455137  

I have been thinking about this and there seems to be a simpler way of avoiding the extra `std::function` that I haven't considered earlier. First we define a special node value in `node.hpp`  
  
```cpp  
constexpr resp3::node_view done_node{resp3::type::invalid, -1, -1, {}};  
```  
  
then call the adapter with this node when the parser is done [here](https://github.com/boostorg/redis/blob/adf17f2b3b2eb119abe324f82dc93cea0a2d0065/include/boost/redis/resp3/parser.hpp#L97)  
  
```cpp  
template <class Adapter>  
bool parse(resp3::parser& p, std::string_view const& msg, Adapter& adapter, system::error_code& ec)  
{  
   while (!p.done()) {  
      ...  
   }  
  
   // ---> New adapter call here to inform parsing is finished.  
   adapter(std::make_optional<resp3::parser::result>(done_node), system::error_code());  
  
   return true;  
}  
```  
  
Then call `set_views()` on the adapter when this node is detected [here](https://github.com/boostorg/redis/pull/278/files#diff-5d85143a4edd5e06ac41022496b7a05dd6c7f661b33eb380b23cf8f8dc92079bR204)  
  
```cpp  
template <>  
class general_aggregate<result<flat_response_value>> {  
private:  
   result<flat_response_value>* result_;  
  
public:  
   template <class String>  
   void operator()(resp3::basic_node<String> const& nd, system::error_code&)  
   {  
      // ---> Check whether done here.  
      if (nd == done_node) {  
         result_->value().set_views();  
         return;  
      }  
  
      ...  
   }  
};  
```  
  
I totally missed this possibility earlier but it looks cleaner than adding a new `std::function`? Do you mind trying this?

> Username: D0zee  
> Created_at: 2025-07-06 20:46:04 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2188593907  

Yes, it looks much better and we don't have to care about heap allocations. I will try it!

> Username: D0zee  
> Created_at: 2025-07-09 13:05:06 UTC  
> Updated_at: 2025-07-09 13:05:07 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2194987582  

Am I correct that this case must be handled by others adapters as well?

> Username: mzimbres  
> Created_at: 2025-07-10 07:35:47 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2196860653  

Hi @D0zee, yes other adapters will have to handle this as well, for example  
```cpp  
if (nd == done_node)  
    return;  
```  
I forgot to say this in my previous comment.

> Username: mzimbres  
> Created_at: 2025-07-10 09:18:56 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2197097993  

Hi again, there is still one problem to solve. The way I suggested above will cause the `set_view` to be called multiple times, once for each request in the pipeline. That is because the same flat response can be used to store the response of multiple requests. I am still not sure what is the simplest way to avoid that, it can be solved by adding state to `set_views()` so that it does not traverse what has already been traversed.  
  
Or perhaps there is a way to wrap the adapters at other location such as [here](https://github.com/boostorg/redis/blob/develop/include/boost/redis/adapter/any_adapter.hpp#L44-L51)  
  
```cpp  
   template <class T>  
   static auto create_impl(T& resp) -> impl_t  
   {  
      using namespace boost::redis::adapter;  
      auto adapter = boost_redis_adapt(resp);  
      std::size_t size = adapter.get_supported_response_size();  
      return {std::move(adapter), size};  
   }  
```  
or [here](https://github.com/boostorg/redis/blob/develop/include/boost/redis/impl/multiplexer.ipp#L22-L25)  
```cpp  
   adapter_ = [this, adapter](resp3::node_view const& nd, system::error_code& ec) {  
      auto const i = req_->get_expected_responses() - remaining_responses_;  
      adapter(i, nd, ec);  
   };  
```  
so that only the last call to `adapter(done_node, ec)` triggers a `set_view()`. I think however this might be too complicated and perhaps it just simpler to let each call to set_view to traverse only what has not been set yet, as suggested above.  
  
If I have time I will review all of this to see if there is any simplification possible.


---

## Comment 11

> Username: anarthal  
> Created_at: 2025-07-10 10:36:46 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3056853868  

I might be late to the discussion, but the new API looks more inconvenient than what was proposed initially, leaking much many implementation details and requiring workarounds in the adapters. I guess there's a strong reason performance-wise to go this way. Do we have measures on how faster this approach is vs. the more convenient one?

---

## Comment 12

> Username: mzimbres  
> Created_at: 2025-07-10 11:04:33 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3056954325  

@anarthal There are two problems to solve  
  
1. The `generic_flat_response` api  
2. Where to construct the `string_views` from the offsets.  
  
Number 1. is the easier part and I would like to have it equal to `generic_response`. The current PR is almost there but actually requires calling `resp.view()`. I am still not sure whether this is ok but it is something easy to change.  
  
Number 2. is what is causing problems. I am still trying to understand what is cleaner from the design and performance point of view and in a way that doesn't break existing code. We have already two callbacks associated with `async_exec`, the adapter (and its wrapper) and the `set_done_callback`. The latter is the best place where we can call `flat_resp.set_view()` but type erasure is preventing that. Adding a new callback is a poor workaround which makes the code more messy. The third option is to let parser notify the adapter it is done.  
  
I think the third option is the only clean and sound option because it makes sense to know when parsing is complete from the adapter. But once we have that the `set_done_callback` existence become perhaps unnecessary.  
  
Given this complexity I think it would be simpler to split this PR in two. @D0zee works only on 1. and I will work on part 2. so he has a sane way of calling `set_views()` that does not mess with the code. After I am done he can rebase and it should work.

---

## Comment 13

> Username: D0zee  
> Created_at: 2025-07-10 12:14:04 UTC  
> Updated_at: 2025-07-12 14:02:28 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3057197783  

@mzimbres Let me please know when your part is ready. I think I will finish my part (`generic_flat_response` API) tomorrow and on weekends

---

## Comment 14

> Username: mzimbres  
> Created_at: 2025-07-10 13:17:11 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3057422216  

@D0zee I will. I am currently finishing [this](https://github.com/boostorg/redis/pull/283) PR and after it is merged I will investigate how to solve this one.

---

## Comment 15

> Username: anarthal  
> Created_at: 2025-07-10 16:24:29 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3058125629  

>We have already two callbacks associated with async_exec, the adapter (and its wrapper) and the set_done_callback.   
  
Note that the done callback currently belongs to the I/O world (it interacts with a channel) rather than the parser world. IMO long-term the callback should be replaced by a reader action (as I think was your intention according to [this comment](https://github.com/boostorg/redis/blob/adf17f2b3b2eb119abe324f82dc93cea0a2d0065/include/boost/redis/impl/reader_fsm.ipp#L59-L64)).  
  
Semantically, having adapters support a "done" callback looks sound to me. But as you said, there is the type erasing issue.  
  
If you want, I can try to write a type-erased adapter type that encapsulates both functions, something like:  
  
```cpp  
class any_adapter_impl {  
    // stores the underlying adapter, akin to what std::function does for functions  
public:  
    void on_node(std::size_t, resp3::node_view const&, system::error_code&);  
    void on_finished();  
};  
```  
  
Then you can use this type in `parser`.

---

## Comment 16

> Username: mzimbres  
> Created_at: 2025-07-10 20:17:16 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3058921456  

> Note that the done callback currently belongs to the I/O world (it interacts with a channel) rather than the parser world. IMO long-term the callback should be replaced by a reader action (as I think was your intention according to this comment).  
  
Yeah, that is an important realization. Extending the adapter with `on_finished` is only meant to aid building the response from the wire protocol.  
  
> Semantically, having adapters support a "done" callback looks sound to me.  
  
I think we need three functions `on_star()`, `on_node()` and `on_finish()`. We actually have `on_start()` that is currently called [on_value_available](https://github.com/boostorg/redis/blob/adf17f2b3b2eb119abe324f82dc93cea0a2d0065/include/boost/redis/adapter/detail/adapters.hpp#L258).  
  
> But as you said, there is the type erasing issue.  
  
There is a simple way to deal with that. We can add a new parameter to the adapter, currently we have  
  
```cpp  
template <class String>  
void operator()(resp3::basic_node<String> const&, system::error_code&);  
```  
  
which could be changed to  
  
```cpp  
enum class event { start, node, finish};  
  
template <class String>  
void operator()(event ev, resp3::basic_node<String> const& nd, system::error_code& ev)  
{  
   switch (ev) {  
      case start: adapter.on_start(); return;  
      case node: adapter.on_node(nd, ev); return;  
      case finish: adapter.on_finish(); return;  
   }  
}  
```  
That would be a breaking change but probably nobody is writing adapters.  
  
> If you want, I can try to write a type-erased adapter type that encapsulates both functions, something like:  
  
You are welcome, the adapter module has its complexity so feel free to ask. Also, please create a sub-issue in the corresponding ticket.  
  
> class any_adapter_impl {  
>     // stores the underlying adapter, akin to what std::function does for functions  
> public:  
>     void on_node(std::size_t, resp3::node_view const&, system::error_code&);  
>     void on_finished();  
> };  
  
Note that `on_finished` has to be called when each response is finished and not only once when the all responses were received. For example  
  
```cpp  
request req  
req.push("COMMAND1", ...);  
req.push("COMMAND2", ...);  
req.push("COMMAND3", ...);  
  
response<T1, T2, T3> resp;  
  
co_wait conn.async_exec(request, resp);  
  
// on_finish will have been called three times when we get here, once  
// for each response element.  
```  
  
I am noticing how much background knowledge this implementation requires, I should not have expected @D0zee to go through all these details, apologies.

---

## Comment 17

> Username: D0zee  
> Created_at: 2025-07-12 16:28:46 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3065834923  

Hi @mzimbres, I've resolved the comments above and added api methods required for tests/examples. However I realized that examples don't work because we have to do the same trick as with `set_done_callback` in `multiplexer` [here](https://github.com/boostorg/redis/blob/adf17f2b3b2eb119abe324f82dc93cea0a2d0065/include/boost/redis/detail/multiplexer.hpp#L174).   
  
With adapter extension mentioned below we will be able to get rid of `prepare_done` callback in `any_adapter` and trick I mentioned below will be not needed. Looking forward to the implementation, it would unblock me. Thank you in advance!

---

## Comment 18

> Username: mzimbres  
> Created_at: 2025-07-16 21:08:16 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3080779097  

@D0zee Thanks. We are working on the prerequisites for this PR and will notify you when it is finished so we can proceed with this one.

---

## Comment 19

> Username: mzimbres  
> Created_at: 2025-08-10 16:18:22 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3172745566  

Hi @D0zee, the parse-events PR is finally merged. you can rebase now and add `on_init()`, `on_node()` and `on_done()` functions to your adapter.

---

## Comment 20

> Username: D0zee  
> Created_at: 2025-08-11 05:20:06 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3173303363  

Hi @mzimbres, thank you for letting me know! I will tweak PR this week

---

## Comment 21

> Username: D0zee  
> Created_at: 2025-08-18 20:01:39 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3198242879  

Hi guys, can you please have one more look at PR? After rebase I've replaced almost all `generic_response` occurrences with `generic_flat_response` and added implementation of `on_init()/on_done()/on_node()` methods. All affected tests are passed. All tweaked examples work correctly if I didn't miss anything. Sorry for the delay!

---

## Review 22 [Commented]

> Username: mzimbres  
> Created_at: 2025-08-23 22:12:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/278#pullrequestreview-3148959272  

📁 include/boost/redis/adapter/detail/adapters.hpp

```diff
 189 |+    {
 190 |+       if (result_->has_value()) {
 191 |+          result_->value().set_view();
```

> Username: mzimbres  
> Created_at: 2025-08-23 22:12:31 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2296347777  

If a request contains multiple commands, `on_done()` and by consequence `set_view()` will be called multiple times, although it should be called only once on the last `on_done()` call. I have to review the code again to understand whether we know upfront how many times `on_done()` will be called. Traversing the vector of nodes every time is prohibitively expensive.  
  
This might get more complex when reading server pushes with async_receive since we don't really know how many we are expecting.

> Username: D0zee  
> Created_at: 2025-08-24 19:36:24 UTC  
> Url: https://github.com/boostorg/redis/pull/278#discussion_r2296781656  

Actually I have not thought about it, I will consider this case as well


---

## Comment 23

> Username: mzimbres  
> Created_at: 2025-08-23 22:19:24 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3217447756  

Hi @D0zee, thanks again. I did not have the time to review last week. While reading the code now it occurs me we need a way to avoid calling `set_view()` on every `on_done()` since that would be prohibitively expensive. I have to review the code again.  
  
I might no have the time next week, but after that I do, so please have some patience. Thanks.

---

## Comment 24

> Username: mzimbres  
> Created_at: 2025-08-31 21:55:58 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3240439859  

Hi again, regarding the problem of calling `set_view()` only at the last `on_done()` parse event: This is probably simple to solve if if we pass [the number of expected responses](https://github.com/boostorg/redis/blob/da2f0101d01cf45b848a11a8936acf1d47cc5b4e/include/boost/redis/request.hpp#L87) to the adapter. However, when dealing with server pushes we don't know how many will be received upfront so the library would have to call `set_view()` right before resuming `async_receive()`, which would be probably [here](https://github.com/boostorg/redis/blob/da2f0101d01cf45b848a11a8936acf1d47cc5b4e/include/boost/redis/connection.hpp#L294), but we can't since we only have a type erased push-receiver adapter.   
  
I think I also oversaw this problem in the first stages of this PR that used the set_done_callback, that only works for request/response but not for server pushes.  
  
In summary, it might be better to delegate the `set_view()` call to the user instead of finding a way to make the connection or adapter call it on behalf thereof. If we can agree on that I would like to revert the changes to the examples that replace all `generic_response` occurrences with `generic_flat_response`. We should use only one example for now and comment on the need to call `set_view()`.  
  
Thoughts?

---

## Comment 25

> Username: anarthal  
> Created_at: 2025-09-01 09:22:20 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3241576165  

Pushing the responsibility to the user seems hostile. The library seems the right place to do it.  
  
If the `on_finished` adapter callback does not suit this use case, what's its use then? Shouldn't it be either removed or replaced by something that does suit (e.g. `on_all_finished`)?  
  
On the other hand, why was the idea of creating the `string_view` objects on demand (i.e. a custom range) ruled out? Has the "less efficient" call been backed by any actual data?

---

## Comment 26

> Username: criatura2  
> Created_at: 2025-09-04 07:53:04 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3252377090  

After some days thinking I believe there is a good solution that just requires making `add_node` and `set_view` smarter.  
  
  1. Add a new field `std::size_t pos_ = 0` to `flat_response_value`.  
  
  2. Let `set_view` use `pos_` to set the views incrementally when it gets called by `on_done`  
  
```cpp  
void set_view()  
 {  
    for (; pos_ < view_.size(); ++pos_) {  
       auto& v = view_.at(pos_).value;  
       v.data = std::string_view{data_.data() + v.offset, v.size};  
    }  
 }  
```  
  
  3. Move the current `add_node` implementation to `add_node_impl` and let `add_node` detect any memory allocations  
  
```cpp  
void add_node(resp3::basic_node<String> const& nd)  
{  
  auto capacity_before = data_.capacity();  
  add_node_impl(nd);  
  auto capacity_after = data_.capacity();  
  
  if (capacity_after > capacity_before)  
    pos_ = 0;  
}  
```  
  
That means, every time `on_done` calls `set_view` it will only traverse nodes that haven't been set yet. When a memory reallocation is detected `pos_` is set to `0` so that all nodes get reset on the next `on_done` call. Reallocation however will either not occur if the user reserves memory upfront or only a couple of times at program startup since responses are reused, meaning that asymptotically it will not allocate any memory.  
  
@D0zee Thanks for keeping up so far, this PR has been very helpful in detecting open points in the original design. If you don't have the time to implement the suggestion above I can take over it, I think however this is not much work. Thanks.

---

## Comment 27

> Username: D0zee  
> Created_at: 2025-09-04 09:38:19 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3252824956  

@criatura2 Thank you for your proposal, but I think the similar problem was discussed at some moment of time in this PR and we denied that. The intention is to call `set_view()` only once to be efficient. That's why we need to know at which point we need to call this method.   
  
I agree with @anarthal that the responsibility of setting view must be rather on library than on client. I will spend some time thinking about this problem on FSM side.

---

## Comment 28

> Username: criatura2  
> Created_at: 2025-09-04 09:55:16 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3252884140  

> @criatura2 Thank you for your proposal, but I think the similar problem was discussed at some moment of time in this PR and we denied that. The intention is to call set_view() only once to be efficient.  
  
What is not ok is to traverse the whole vector for each `on_done` call, which is a O(n^2) solution. It is ok however to traverse only the newly added items, which is an amortized O(n) solution. And as said previously, since re-allocations are very rare or non-existent, the solution would be close to optimal.

---

## Comment 29

> Username: D0zee  
> Created_at: 2025-09-04 10:28:10 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3252991597  

> What is not ok is to traverse the whole vector for each on_done call, which is a O(n^2) solution.  
  
You are right, that is the next step I'm going to look at. It was noticed here https://github.com/boostorg/redis/pull/278#issuecomment-3217447756.  
  
Ideally we would like to call `set_view()`only once and I think it is possible, just don't have time this week to dive deeper.   
  
I suspect your solution to be `O(n * log(n))` where `n` is the number of response strings in `generic_flat_response`, because of resetting `pos_` when reallocation happens. We can assume that it happens log(n) times in general case (for different implementations of strings the load factor might be different):  
  
```c++  
  if (capacity_after > capacity_before)  
    pos_ = 0;  
```  
  
@criatura2  If you would like to help you can check the comments below and think about the case with server pushes when we don't know how many messages to expect (details are here: https://github.com/boostorg/redis/pull/278#issuecomment-3240439859). Next week I'm going to get deeper and try to come up with solution.

---

## Comment 30

> Username: criatura2  
> Created_at: 2025-09-04 10:43:15 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3253050886  

> I suspect your solution to be O(n * log(n)) where n is the number of response strings in generic_flat_response, because of resetting pos_ when reallocation happens.  
  
If the response is reused such as [here](https://github.com/boostorg/redis/blob/2133ed747b246c5c9278c1c88a334d10ad4a0036/example/cpp20_subscriber.cpp#L57) only one read will be `O(n * log(n))` after that it gets `O(n)`. Also, something like  
  
```cpp  
generic_flat_response resp;  
resp.value().reserve(some_number);  
```  
Will bring reallocations to negligible levels.

---

## Comment 31

> Username: D0zee  
> Created_at: 2025-09-04 11:01:21 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3253136395  

You have come up to the same problem - how to learn the number of responses we are going to handle. The problem is that in your approach we also need to know what is the average or expected size of each response. In my opinion it is impossible to know in advance because it depends on the content stored in redis-server.  
  
In suggested below approach to call `set_view` on the last response we need to know only if this response is last. I believe that it is possible derive somehow in comparison to the content of redis-server.

---

## Comment 32

> Username: criatura2  
> Created_at: 2025-09-04 11:14:15 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3253196656  

There is no real difference between calling `set_view()` only once and calling it incrementally on each `on_done` call. Knowing the number of responses e.g. the number of `on_done` calls is actually not necessary (and not available for server pushes)  
  
Do you agree that the number of reallocations is negligible?

---

## Comment 33

> Username: D0zee  
> Created_at: 2025-09-08 07:58:27 UTC  
> Updated_at: 2025-09-08 08:00:10 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3265059083  

What if we indeed move the responsibility to call `set_view` method to the user, but we will do that implicitly? In each API method of `generic_flat_response` we will check if the structure is ready for use, if it is not - we call `set_view`. API is used only by clients and only when all operations with Redis are done. Thus, first API call will automatically set up views.  
  
@mzimbres @anarthal What do you think?

---

## Comment 34

> Username: anarthal  
> Created_at: 2025-09-08 15:36:29 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3266878915  

Are you proposing the following scheme?  
  
```cpp  
iterator begin() const {  
   if (!views_set_) {  
      set_views();  
      views_set_ = true;  
   }  
   return /* whatever */  
}  
```

---

## Comment 35

> Username: D0zee  
> Created_at: 2025-09-09 05:20:45 UTC  
> Updated_at: 2025-09-09 11:20:49 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3268914381  

@anarthal yes, exactly. In our case this check will be at the beginning of this methods of `generic_flat_response`:  
  
<img width="677" height="131" alt="Screenshot 2025-09-09 at 3 19 48 PM" src="https://github.com/user-attachments/assets/afd61d07-e9d2-415a-936a-c114b2e70bb5" />

---

## Comment 36

> Username: anarthal  
> Created_at: 2025-09-10 10:33:39 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3274340773  

Hm. This has a number of problems:  
  
* `begin()` and `end()` are now either non-const, or need to use a mutable variable underneath.  
* `begin()`, `end()` and similar functions are no longer guaranteed to be "cheap".  
* I don't know if this is a concern, but all of these functions now have an extra branch, which may hurt performance.  
  
I want to understand why this can't be done after parsing is finished - is it because of pushes? For regular requests, it looks like the points where parsing starts and ends are clearly defined.

---

## Comment 37

> Username: anarthal  
> Created_at: 2025-09-10 10:36:50 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3274349760  

Also, if the problem are indeed pushes, what are the plans to implement `consume_one`? It looks like the recommended way to manage pushes, although the implementation for `generic_response` already looks inefficient.

---

## Comment 38

> Username: mzimbres  
> Created_at: 2025-09-13 16:37:17 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3288611525  

> I want to understand why this can't be done after parsing is finished - is it because of pushes?  
  
Yes. The default size of the channel used to deliver pushes is 256, that means `on_done()` can be called that many times and you never know which one is the last call so that `set_view()` can be called. We might want to call `set_view` right after `try_send` and just before `async_send` [here](https://github.com/boostorg/redis/blob/0cf2441ed2b81506f4b6f626d01ea43785ba31a9/include/boost/redis/connection.hpp#L295) but that does not work either because that would be a race condition i.e. the receiver side might suspend allowing a further push to be processed after `set_view` has been called.

---

## Comment 39

> Username: mzimbres  
> Created_at: 2025-09-13 16:39:02 UTC  
> Updated_at: 2025-09-13 16:39:53 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3288612862  

@D0zee sorry for late reply, I was very busy these days. As I said earlier (in my other account criatura2). Detecting allocations is  close to optimal IMO. I don't like the idea of safe-guarding every function with an if.

---

## Comment 40

> Username: D0zee  
> Created_at: 2025-09-13 20:18:45 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3288801707  

Hi @mzimbres, didn't know that account is yours! I need to think about it one more time. Sorry I was busy this week as well. Thank you for bearing with me!

---

## Comment 41

> Username: D0zee  
> Created_at: 2025-10-01 04:48:48 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3354732272  

> If you don't have the time to implement the suggestion above I can take over it, I think however this is not much work. Thanks.  
  
  
Hi @mzimbres, can you please take over that change? I understand that I've been extremely busy recently and it stops this PR to be merged.

---

## Comment 42

> Username: mzimbres  
> Created_at: 2025-10-03 09:12:18 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3364901681  

> Hi @mzimbres, can you please take over that change? I understand that I've been extremely busy recently and it stops this PR to be merged.  
  
Hi @D0zee, no problem I will pick your commits to properly attribute your the work you did. I will also pull you in the PR when it is ready. Thanks.

---

## Comment 43

> Username: mzimbres  
> Created_at: 2025-10-19 11:31:59 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3419593050  

@D0zee, what email address would you like to use in the copyright notice [here](https://github.com/boostorg/redis/blob/263-marcelo/include/boost/redis/generic_flat_response_value.hpp#L2-L3)? Thanks

---

## Comment 44

> Username: D0zee  
> Created_at: 2025-10-21 15:34:11 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3427295841  

Hi @mzimbres, nvladimirov.work@gmail.com. Thank you!

---

## Comment 45

> Username: mzimbres  
> Created_at: 2025-11-18 13:16:24 UTC  
> Url: https://github.com/boostorg/redis/pull/278#issuecomment-3547614535  

This is now merged in this PR https://github.com/boostorg/redis/pull/340

---
