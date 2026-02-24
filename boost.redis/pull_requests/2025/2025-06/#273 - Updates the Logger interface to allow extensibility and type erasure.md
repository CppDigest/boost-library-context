# #273 Updates the Logger interface to allow extensibility and type erasure [Merged]

> Username: anarthal  
> Created at: 2025-06-15 16:49:56 UTC  
> Updated at: 2025-06-23 10:07:26 UTC  
> Merged at: 2025-06-23 10:07:21 UTC  
> Closed at: 2025-06-23 10:07:21 UTC  
> Url: https://github.com/boostorg/redis/pull/273  

Removes all the logger::on_xxx functions  
Removes the Logger template parameter to async_run  
Adds a logger constructor that allows passing a std::function to customize logging behavior  
Adds constructors to connection and basic_connection taking a logger  
Deprecates config::logger_prefix  
Deprecates the async_run overload taking a logger parameter  
Deprecates the basic_connection::async_run overload not taking any config object  
Deprecates the basic_connection::next_layer_type typedef  
Makes the default log level logger::info  
Makes the logging thread-safe  
Cleans up deprecated functionality from examples  
Adds docs on logging  
Adds an example on how to integrate spdlog into Boost.Redis logging  
  
close #213

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-06-15 16:55:42 UTC  
> Url: https://github.com/boostorg/redis/pull/273#issuecomment-2974257899  

@mzimbres This addresses user requests for logger extensibility, and provides an easy way to type erase loggers, which I need to keep pushing the sans-io stories forward.  
  
I've tried to make the design dead simple - just a (log level, string) callback. I've checked spdlog and it can be integrated easily with this design. Regarding efficiency, it should be similar (if not better), since most of the types that we used to stream internally invoke a conversion to `std::string` as part of their `operator<<` implementation.  
  
Before I go ahead and write tests and docs for this - do you think that the design is alright? Any feedback?  
  
Also, I'm thinking of replacing `std::clog` by `std::printf`, which I think is thread-safe (while clog is not).

---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2025-06-17 07:26:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/273#pullrequestreview-2934454328  

📁 include/boost/redis/connection.hpp

```diff
 522 |-    auto async_run(config const& cfg = {}, Logger l = Logger{}, CompletionToken&& token = {})
 515 |+    template <class CompletionToken = asio::default_completion_token_t<executor_type>>
 516 |+    auto async_run(config const& cfg = {}, logger l = {}, CompletionToken&& token = {})
```

> Username: mzimbres  
> Created_at: 2025-06-17 07:26:07 UTC  
> Updated_at: 2025-06-17 07:26:08 UTC  
> Url: https://github.com/boostorg/redis/pull/273#discussion_r2151525746  

I think that if the logger becomes a class member we should remove it form `async_run` and set it elsewhere i.e. constructor or `set_logger`, otherwise I am afraid `async_exec` and `async_run` might race e.g. user first call `async_exec` when no logger is set.

> Username: anarthal  
> Created_at: 2025-06-20 11:05:01 UTC  
> Updated_at: 2025-06-20 11:05:02 UTC  
> Url: https://github.com/boostorg/redis/pull/273#discussion_r2158697119  

We currently don't log anything in async_exec. If you're OK with it, I will add it as a constructor argument, and deprecate passing it to `async_run`. And we won't log anything in async_exec until we remove the deprecated function.


---

## Comment 3

> Username: mzimbres  
> Created_at: 2025-06-17 08:03:39 UTC  
> Url: https://github.com/boostorg/redis/pull/273#issuecomment-2979348219  

I think the current approach used for logging does not scale well since we have to add a new function everywhere logging is required.  That means, everytime the code is refactored or a new features such as sentinel and cluster support is added, the logger interface will have to be changed or externed. Sometimes it might be necessary to log types from the `detail` namespace which cannot appear in the logging interface.  
  
Considering this I would like to make the log interface stupid, for example  
  
```cpp  
struct logger {  
   // Syslog levels.  
   enum class level {disabled, emerg, alert, crit, err, warning, notice, info, debug};  
  
   // Log object passed by the user.  
   std::function<void(level, std::string_view)> log;  
  
   // User decides up to which level he wants to see messages.  
   level level_;  
};  
```  
  
and use it like  
  
```cpp  
logger l;  
  
l.log = [](level l, std::string_view msg)  
{  
   if (l >= level_)  
      std::clog << msg << std::endl;  
};  
  
l.level_ = logger::level::crit;  
```  
  
Once the logger is this stupid Boost.Redis would then only have to care about message formatting and we can do that on our side with macros to avoid parameter evaluation, for example  
  
```cpp  
#define BOOST_REDIS_LOG_ERROR_CODE(logger, level, ec)\  
   if (level >= logger_.get_max_level() && ec) {\  
      std::string tmp;\  
      tmp += ec.message();\  
      tmp += " [";\  
      tmp += ec.to_string();\  
      tmp += ']';\  
      logger.log(tmp)\  
   }\  
```  
  
What do you think? Can we take that direction in this PR?

---

## Comment 4

> Username: anarthal  
> Created_at: 2025-06-17 10:05:40 UTC  
> Url: https://github.com/boostorg/redis/pull/273#issuecomment-2979754759  

This is absolutely the direction it is already taking. The logging functions are actually in the detail namespace, so they're no longer part of the interface. My plan is gradually removing them as we migrate things to the sans-io approach. Each fsm would then be responsible for formatting. I've just left them with a similar interface as we used to have to reduce the amount of changes, and because fsms don't exist yet for the algorithms that log.

---

## Comment 5

> Username: mzimbres  
> Created_at: 2025-06-17 18:33:10 UTC  
> Url: https://github.com/boostorg/redis/pull/273#issuecomment-2981426672  

Ok. Then lets us go forward with this plan.

---

## Comment 6

> Username: anarthal  
> Created_at: 2025-06-22 16:28:21 UTC  
> Updated_at: 2025-06-22 16:28:29 UTC  
> Url: https://github.com/boostorg/redis/pull/273#issuecomment-2994304353  

@mzimbres  This is now complete. I've added the following:  
  
- I've split all on_xxx logging functions into a class in the detail namespace and in a separate file, to make it clear that they're private.  
- I've added an example on how to integrate spdlog.  
- I've added constructors to connection taking a logger.  
- I've decreased the default logging level to info, which I think is a reasonable default.  
- I've made logging thread-safe.  
- Added tests and docs.  
- Deprecated the async_run overload taking a logger.  
  
If you find this OK, I can merge it so you can build your read_fsm PR on top of this.

---

## Review 7 [Commented]

> Username: mzimbres  
> Created_at: 2025-06-22 19:15:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/273#pullrequestreview-2948283118  

📁 include/boost/redis/connection.hpp

```diff
 761 |-    template <class, class> friend class detail::run_op;
 828 |+    // Used by both this class and connection
 829 |+    void set_run_logger(const logger& lgr, const config& cfg)
```

> Username: mzimbres  
> Created_at: 2025-06-22 19:15:57 UTC  
> Updated_at: 2025-06-22 19:15:58 UTC  
> Url: https://github.com/boostorg/redis/pull/273#discussion_r2160438321  

Shouldn't this be named `set_logger` since the logger can also be used in `async_exec`?

> Username: anarthal  
> Created_at: 2025-06-22 20:18:02 UTC  
> Url: https://github.com/boostorg/redis/pull/273#discussion_r2160476075  

What I meant with that name is "what async_run calls to set the logger". Note that constructors don't call this function. It is there because calling `basic_connection::async_run` from `connection::async_run`'s implementation caused warnings because of the deprecations. The assignment is special in the sense that it will discard the logging function and only use the level. I can probably find a better name for this.


---
