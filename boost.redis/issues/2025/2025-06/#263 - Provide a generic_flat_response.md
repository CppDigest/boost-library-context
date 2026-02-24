# #263 - Provide a generic_flat_response [Closed]

> Username: mzimbres  
> Created at: 2025-06-02 15:01:42 UTC  
> Updated at: 2025-11-18 13:20:11 UTC  
> Closed at: 2025-11-18 13:20:10 UTC  
> Url: https://github.com/boostorg/redis/issues/263  

For latency sensitive users such as [this](https://github.com/boostorg/redis/issues/246#issuecomment-2927909878) one it might be useful to use a flat data structure to store reponses where buffers can be reused and thereby removing the need for dynamic memory allocations.  
  
A generic_flag_response would keep all its data into `std::string` buffer and refer to it with `string_view`s, for exampl  
  
```cpp  
struct generic_flat_response {  
    std::string buffer;  
    adapter::result<std::vector<resp3::basic_node<std::string_view>> response;  
};  
```

---

## Comment 1

> Username: D0zee  
> Created at: 2025-06-14 19:28:20 UTC  
> Updated at: 2025-06-15 12:47:35 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-2973035216  

Hi Marcelo, I would like to try solving this issue. Can you please point out what should I learn/read that can help to implement `generic_flat_response`? My initial plan is to look at use cases of `generic_response` across all repo and understand how to add a new response type in an elegant way. I would appreciate any sources if you provide them.

---

## Comment 2

> Username: mzimbres  
> Created at: 2025-06-15 13:18:32 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-2973834925  

Hi @D0zee thanks, you are welcome. I will try to come up with some explanation this week. The flat response type should have the same interface as the `generic_reponse`. It will also likely use the same adapter of the `generic_response`.

---

## Comment 3

> Username: mzimbres  
> Created at: 2025-06-19 20:29:24 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-2989095071  

Hi, here are some details. The code is already generic enough so that if I define the `generic_flag_response` like  
  
```cpp  
using generic_response = adapter::result<std::vector<resp3::node>>;  
using generic_flat_response = adapter::result<std::vector<resp3::node_view>>;  
```  
  
and use it in the `cpp20_subscriber.cpp` example replacing the `generic_response` (and commenting the `consume_one` function) the code will compile. But of course that won't work because the data the `string_view`s points to will have been erase by the time it is used, therefore, what we have to do is to defined the flat response as  
  
```cpp  
using generic_flat_response = adapter::result<flat_response_imp>;  
```  
  
where  
  
```cpp  
class flat_response_impl {  
public:  
   // This API has to be implemented: at(), reserve, begin, end, operator[] etc.  
  
   // And to push data into the container push_back().  
  
private:  
   std::string data_;  
   std::vector<resp3::node_view> view_;  
};  
```  
  
Once we have that you can write an specialization almost identical to what I do for [general_aggregate](https://github.com/boostorg/redis/blob/develop/include/boost/redis/adapter/detail/adapters.hpp#L141-L171) but that copies the data to the `data_` member, which should happen before pushing data into the container [here](https://github.com/boostorg/redis/blob/89a42dbf74819598c3d7bf45870b0e23c2cbf0df/include/boost/redis/adapter/detail/adapters.hpp#L163).  
  
Also, care should taken when pushing data to the string causes it to reallocate and therefore invalidate `string_view`'s. Perhaps it is going to be necessary to work with offsets into the buffer. I am not sure if `node_view` will be suitable for that or a new node type will have to be introduced.

---

## Comment 4

> Username: anarthal  
> Created at: 2025-06-19 21:24:48 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-2989170014  

In case it helps somehow, Boost.MySQL does something somehow similar here: https://github.com/boostorg/mysql/blob/develop/include/boost/mysql/detail/row_impl.hpp  
  
Strings are stored as offsets while parsing, then converted to string_views after no more reallocations may happen.

---

## Comment 5

> Username: D0zee  
> Created at: 2025-06-22 14:14:26 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-2994238351  

thank you for explanations, will dig into them

---

## Comment 6

> Username: D0zee  
> Created at: 2025-06-23 18:06:34 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-2997404030  

May be a silly question, but how properly to build the repo? Tried to use `tools/ci.py` but failed. Can you please help me in that?

---

## Comment 7

> Username: mzimbres  
> Created at: 2025-06-23 20:33:00 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-2997862793  

Hi @D0zee, you can clone boost  
  
```bash  
$ git clone --recurse-submodules https://github.com/boostorg/boost  
  
# I am not sure if the step above will checkout the develop branch so you might have to run  
$ git submodule foreach --recursive git checkout develop  
```  
then create a directory to build it somewhere and run  
  
```bash  
$ cmake <path>/boost/libs/redis -DCMAKE_BUILD_TYPE=Debug  
$ make  
```

---

## Comment 8

> Username: anarthal  
> Created at: 2025-06-26 09:42:14 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-3007863401  

You will probably need to add a `BUILD_TESTING` definition to your CMake invocation for it to build tests:  
  
```  
cmake <path>/boost/libs/redis -DCMAKE_BUILD_TYPE=Debug -DBUILD_TESTING=ON  
```  
  
To run the tests, you need a properly configured Redis server. Under Linux, you can use Docker Compose with the file in `tools/docker-compose.yml`:  
  
```  
IMAGE=ubuntu:22.04 docker compose -f <path>/boost/libs/redis/tools/docker-compose.yml up -d  
```  
  
This will spawn a listening server with adequate TLS configuration and a listening UNIX socket for the UNIX socket tests. You can also have a look at this file and run the redis-server command manually, emulating what this file does.

---

## Comment 9

> Username: D0zee  
> Created at: 2025-06-27 08:04:13 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-3012109909  

Thank you for comments! I was able to set up build environment

---

## Comment 10

> Username: D0zee  
> Created at: 2025-06-27 09:43:30 UTC  
> Updated at: 2025-06-27 11:39:05 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-3012381847  

Hi @mzimbres, I'm thinking about ergonomic API access and wanted to discuss it with you. Right now I really think about implementation of new type of node - `offset_node` derived from `node_view`. This node will expose two additional fields: `size` and `offset` for reconstruction of `std::string_view` from `data_`. I have several questions:  
  
- Should user be able to change the internal state of `flat_response_impl`? In my opinion the state of this structure should be frozen after parser completed its operation.  
  
- Should we provide the opportunity to preallocate the size of response to the user? I assume that yes, but in this case there is a question how to determine the average / median node value size? Also, there is an option to give this responsibility to determine the desired size of each string to the user.  
  
- Do we need to tweak/generalize/write a specialization for `consume_one` function?

---

## Comment 11

> Username: D0zee  
> Created at: 2025-06-27 19:40:30 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-3014189864  

By the way you can have a look at draft implementation of this issue https://github.com/boostorg/redis/pull/278. Please do not judge strictly!

---

## Comment 12

> Username: mzimbres  
> Created at: 2025-06-28 09:38:47 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-3015145521  

Thanks, I will have a look at the PR tomorrow.  
  
> Right now I really think about implementation of new type of node - offset_node derived from node_view.  This node will expose two additional fields: size and offset for reconstruction of std::string_view from data_.  
  
I am not sure we really need a new node type. For example, we could introduce an `offset_string_view`  
  
```cpp  
struct offset_string {  
   std::size_t offset;  
   std::size_t size;  
  
   std::string_view to_string_view(char const* base_ptr) const  
   {  
      return std::string_view{base_ptr + offset, size};  
   }  
};  
```  
  
Then the node definitions would be  
  
  
```cpp  
using node = basic_node<std::string>;  
using node_view = basic_node<std::string_view>;  
using offset_node = basic_node<offset_string>;  
```  
  
I would like to avoid working with temporaries like here  
  
```cpp  
resp3::node_view at(std::size_t) { ... }  
```  
  
and instead we would have  
  
```cpp  
offset_node const& at(std::size_t) const { ... }  
```  
  
That is however not very ergonomic to the user as to get a `string_view` he would have to call  
  
```cpp  
auto data = resp.at(i).to_string_view(resp.get_base_ptr());  
```  
We could also store the base pointer in the `offset_string`  
```cpp  
struct offset_string {  
   char const* base_ptr;  
   std::size_t offset;  
   std::size_t size;  
  
   operator std::string_view() const  
   {  
      return std::string_view{base_ptr + offset, size};  
   }  
};  
```  
  
and set the `base_ptr` once done with the response, for example in the [notify_done](https://github.com/boostorg/redis/blob/b82224700e1396b2d588881c6c6c386d8064b412/include/boost/redis/detail/multiplexer.hpp#L44) callback. To do that we would have to add a new adapter method that would be called [here](https://github.com/boostorg/redis/blob/b82224700e1396b2d588881c6c6c386d8064b412/include/boost/redis/connection.hpp#L698), so instead of  
  
```cpp  
      info->set_done_callback([notifier]() {  
         notifier->try_send(std::error_code{}, 0);  
      });  
```  
  
we would have  
  
```cpp  
      info->set_done_callback([notifier, adapter]() {  
         adapter.prepare_done(); // New adapter method.  
         notifier->try_send(std::error_code{}, 0);  
      });  
```  
  
However, at the moment, the adapter is type erased [here](https://github.com/boostorg/redis/blob/b82224700e1396b2d588881c6c6c386d8064b412/include/boost/redis/adapter/any_adapter.hpp#L50) before we can pass it to the code snippet above  
  
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
Some kind of hopefully small refactoring would be required.  
  
> I have several questions:  
>   
> Should user be able to change the internal state of  
> flat_response_impl? In my opinion the state of this structure should  
> be frozen after parser completed its operation.  
  
I don't have use case to change the state of a response. As a first prototype we can indeed make the interface const.  
   
> Should we provide the opportunity to preallocate the size of  
> response to the user? I assume that yes, but in this case there is a  
> question how to determine the average / median node value size?  
> Also, there is an option to give this responsibility to determine  
> the desired size of each string to the user.  
  
Not sure what you mean, only the user knows the expected size of a response, we can't guess it to preallocate memory.

---

## Comment 13

> Username: D0zee  
> Created at: 2025-06-28 12:32:57 UTC  
> Updated at: 2025-06-28 13:28:07 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-3015224204  

Thank you for the comment!  
  
> I am not sure we really need a new node type. For example, we could introduce an offset_string_view  
  
However in this case the user lacks other meta information like `depth`/`type`/`aggregate_size`. That was a reason why I don't just only string-like structure. I thought this information is important for the end user. However if it is not needed in your opinion the implementation will be much easier.  
  
> I would like to avoid working with temporaries like here  
  
Could you please elaborate on the reason? It's going to be light-weight operation or the performance is not only the reason? In my implementation it is going to be consistent, he will be able to work with the new `generic_flat_response` in the same way as he does with `generic_response`.  
  
> Not sure what you mean, only the user knows the expected size of a response, we can't guess it to preallocate memory.  
  
You are right and I understood it later after I left the comment. If the user assumes about the size of the response and length of each response string he will pass parameters relying on this assumption and instead of X memory allocations during parsing operation (when std::string buffer grows) he would have one memory allocation. It is just opportunity to have less allocations if the user really cares about it.

---

## Comment 14

> Username: mzimbres  
> Created at: 2025-06-28 22:42:17 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-3016118159  

> However in this case the user lacks other meta information like depth/type/aggregate_size. That was a reason why I don't just only string-like structure. I thought this information is important for the end user. However if it is not needed in your opinion the implementation will be much easier.  
  
In your PR you defined the `offset_node` like this  
  
```cpp  
struct offset_node : resp3::node_view {  
   std::size_t offset{};  
   std::size_t size{};  
};  
```  
  
I was wondering however if it is more sound to define an `offset_string` and use that as `basic_node` template parameter  
  
```cpp  
struct offset_string {  
   std::string_view data;  
   std::size_t offset;  
   std::size_t size;  
};  
  
using offset_node = basic_node<offset_string>;  
```  
  
I think this is conceptually more robust because the offset refers only to the string and not to node itself.  
  
> Could you please elaborate on the reason? It's going to be light-weight operation or the performance is not only the reason?  
  
Let us take [this](https://github.com/sewenew/redis-plus-plus/issues/638#issuecomment-2873713909) usecase where the user is reading 109,000 hashes. If we traverse such a response with the function  
  
```cpp  
resp3::node_view at(std::size_t index) { return make_node_view(view_.at(index)); }  
```  
  
we will endup coping `109,000 * sizeof(offset_node)` bytes of data or ~6Mb according to my calculation, this is probably negligible but worth avoiding if possible. Low latency was one of the main design principles behind Boost.Redis.  
  
Sidenote: In the future I would like to have the option to read large responses from the socket directly into the flat response buffer, reducing data copying to the very minimum.  
  
> In my implementation it is going to be consistent, he will be able to work with the new generic_flat_response in the same way as he does with generic_response.  
  
Sure, thats is great. Above I was throing my ideas on the table so we can discuss but I am not sure all of that is reasonable. Tomorrow I will have more time and will have a closer look in your PR.

---

## Comment 15

> Username: D0zee  
> Created at: 2025-07-02 18:42:49 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-3028972948  

I've taken into consideration all comments and suggestions, now the implementation is much cleaner. Please have a look when you find time. Thank you!

---

## Comment 16

> Username: mzimbres  
> Created at: 2025-11-18 13:20:11 UTC  
> Url: https://github.com/boostorg/redis/issues/263#issuecomment-3547629682  

Implemented in https://github.com/boostorg/redis/pull/340
