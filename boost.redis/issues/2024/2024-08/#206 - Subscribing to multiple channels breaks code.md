# #206 - Subscribing to multiple channels breaks code. [Closed]

> Username: IYanakiev34  
> Created at: 2024-08-05 15:33:53 UTC  
> Updated at: 2024-08-09 16:25:14 UTC  
> Closed at: 2024-08-09 16:25:14 UTC  
> Url: https://github.com/boostorg/redis/issues/206  

The goal of the program is to subscribe to multiple channels and listen to client messages at the same time publish messages to different channels. When I issue a subscribe to a single channel the code works as expected, however when I issue subscribe to multiple channels the code breaks and I can never publish requests.   
  
This is the header for the worker which is responsible for serialize messages into json format and then writing them to log and redis.  
```c++  
struct redis_message {  
  std::string topic;  
  std::string msg;  
};  
  
using namespace std::literals;  
  
class worker {  
 public:  
  worker(create_order_chan_type& chan);  
  
  inline void start() { ctx_.run(); }  
  
  void stop() {  
    redis_conn_->cancel();  
  
    if (!ctx_.stopped()) {  
      ctx_.stop();  
      worker_pool_.join();  
    }  
  }  
  
  auto get_executor() noexcept { return ctx_.get_executor(); }  
  
  inline void set_topics_to_subscribe(  
      std::array<std::string, 3> const& topics) {  
    topics_ = topics;  
  }  
  
  [[nodiscard]] inline auto get_topics_to_push() const noexcept  
      -> std::vector<std::string> {  
    std::vector<std::string> res;  
    res.reserve(topics_.size());  
    for (auto const& top : topics_) {  
      res.push_back(top);  
    }  
    return res;  
  }  
  
  /**  
   * @brief Function to be called by external clients of the worker to submit  
   * events to be written to redis and logs.  
   * @tparam Event - The type of the event to write  
   * @param event - An instance of the event type  
   */  
  template <typename Event>  
  auto write(Event&& event) {  
    net::post(worker_pool_, [this, evt = std::forward<Event>(event)]() {  
      std::string msg{  
          boost::json::serialize(boost::json::value_from(evt.value_))};  
      write_to_redis(evt.get_topic_name(), msg);  
      write_to_log(msg);  
    });  
  }  
  
  /**  
   * @brief Entry point for the worker coroutine. It will work until it is  
   * cancelled. It will spin the redis_reader and gateway_writer coroutines.  
   */  
  auto run() -> net::awaitable<void> {  
    using namespace net::experimental::awaitable_operators;  
  
    auto cans = co_await coro::cancellation_state;  
    net::steady_timer timer{ctx_};  
    while (!cans.cancelled()) {  
      try {  
        co_await (redis_reader() && redis_writer());  
        // to not overload the system  
        timer.expires_after(1s);  
        co_await timer.async_wait(net::deferred);  
  
      } catch (sys::system_error& erc) {  
        TR_ERROR(LOG_NAME, "(run) errored out... {0}", erc.what());  
      }  
    }  
  }  
  
  /**  
   * @brief Subscribe to signal channels and listen to client signals. These  
   * signals will be in the form of string messages indicating a specific type  
   * of operation to be performed by the gateway. The signals will be processed  
   * into an internal queue.  
   */  
  auto redis_reader() -> net::awaitable<void>;  
  
  /**  
   * @brief This coroutine will monitor the state of the async circular buffer  
   * storing message and drain it. The messages willn be sent to the appropriate  
   * channels for subscribed clients to listen to.  
   *  
   */  
  auto redis_writer() -> net::awaitable<void>;  
  
 private:  
  /**  
   * @brief Create an object for a redis request to write a message into a  
   * particular topic channel and store it in the internal queue.  
   * @param topic - The topic to which the message will be written to.  
   * @param msg - The message which will be written to the topic.  
   */  
  inline void write_to_redis(std::string const& topic, std::string const& msg) {  
    redis_message_buf_.push(redis_message{topic, msg});  
  }  
  
  /**  
   * @brief Write a message using a log service to either stdout, file, or cloud  
   * service.  
   * @param msg - The msg to be written to the logger.  
   */  
  inline void write_to_log([[maybe_unused]] std::string_view msg) {  
    // NOTE: just use std::clog or comment it out this is not important  
    // TR_INFO(LOG_NAME, "(worker::write_to_log) writing msg: {0}", msg);  
  }  
  
  /* The execution context of the worker */  
  net::io_context ctx_;  
  
  // Thread pool for worker to submit compute work  
  net::thread_pool worker_pool_;  
  
  /* The connection to redis which will be used for pushing subscription  
   * messages and listen to client messages */  
  std::shared_ptr<redis::connection> redis_conn_;  
  
  /* Channel used for communication between the gateway and the worker. Client  
   * requests will be forwarded to the gateway   
   NOTE: you do not need this to reproduce the problem this is work in progress.  
   */  
  [[maybe_unused]] create_order_chan_type& chan_;  
  
  io::async_circular_buffer<redis_message, 128> redis_message_buf_;  
    
  //NOTE topics are injected via a set method not through constructor this is a HACK for now!!  
  std::array<std::string, 3> topics_;  
};  
```  
  
Here are the source files with the respected functionality (ignore the log you can replace them with std::cout I use macros with spdlog for logging)  
```c++  
worker::worker(create_order_chan_type& chan)  
    : ctx_(1),  
      worker_pool_(2),  
      redis_conn_(std::make_shared<redis::connection>(ctx_.get_executor())),  
      chan_(chan),  
      redis_message_buf_(ctx_) {  
  redis::config cfg;  
  cfg.addr.host = "localhost";  // HACK should be configurable  
  cfg.addr.port = "6379";       // HACK should be configrable  
  cfg.health_check_interval = 0s;  
  redis_conn_->async_run(cfg, {redis::logger::level::debug},  
                         net::consign(net::detached, redis_conn_));  
}  
  
auto worker::redis_writer() -> net::awaitable<void> {  
  auto cans = co_await coro::cancellation_state;  
  sys::error_code erc;  
  while (!cans.cancelled()) {  
    auto res = co_await redis_message_buf_.pop();  
    redis::request req;  
    req.push("PUBLISH", res.topic, res.msg);  
    TR_WARN(LOG_NAME, "(redis_write) publishing: {0}", req.payload());  
    co_await redis_conn_->async_exec(  
        req, redis::ignore,  
        net::bind_executor(ctx_, net::redirect_error(net::deferred, erc)));  
    if (erc) {  
      TR_ERROR(LOG_NAME, "(redis_writes) error when publishing: {0}",  
               erc.what());  
      break;  
    }  
  }  
}  
auto worker::redis_reader() -> net::awaitable<void> {  
  redis::request req;  
  // Specify the topics to listen to  
  req.push("SUBSCRIBE", "CHAN1","CHAN2","CHAN3");  
  
  redis::generic_response resp;  
  redis_conn_->set_receive_response(resp);  
  
  while (redis_conn_->will_reconnect()) {  
    TR_INFO(LOG_NAME, "(redis_reader) subscribing...");  
    sys::error_code erc;  
    co_await redis_conn_->async_exec(req, redis::ignore,  
                                     net::redirect_error(net::deferred, erc));  
    if (erc) {  
      TR_ERROR(LOG_NAME, "(redis_reader) error when subscribing... {0}",  
               erc.what());  
      continue;  
    }  
  
    for (sys::error_code erc;;) {  
      co_await redis_conn_->async_receive(  
          net::redirect_error(net::deferred, erc));  
      if (erc) {  
        TR_ERROR(LOG_NAME,  
                 "(redis_reader) error when receiving from channels...");  
        break;  
      }  
  
      TR_INFO(LOG_NAME, "(redis_reader) got: {0}, {1}, {2}",  
              resp.value().at(1).value, resp.value().at(2).value,  
              resp.value().at(3).value);  
  
      resp.value().clear();  
      /**  
       * TODO (ivan) should parse into a struct then serialize into the ring  
       * buffer  
       */  
    }  
  }  
}  
```  
  
I will also supply the code for the async circ buffer:   
```c++  
template <class T, std::size_t Capacity>  
struct async_circular_buffer {  
  using executor_type = boost::asio::io_context::executor_type;  
  
  async_circular_buffer(boost::asio::io_context& exec)  
      : ioc_(exec), cv_(exec), buffer_(Capacity) {  
    cv_.expires_at(boost::asio::steady_timer::time_point::max());  
  }  
  
  void clear() { buffer_.clear(); }  
  
  auto pop() -> boost::asio::awaitable<T> {  
    boost::system::error_code erc;  
    while (buffer_.size() == 0) {  
      co_await cv_.async_wait(boost::asio::redirect_error(boost::asio::deferred, erc)));  
      if (erc) {  
        TR_ERROR(LOG_NAME, "(async_cicruclar_buffer::pop) errored out: {0}",  
                 erc.what());  
      }  
    }  
    auto result = std::move(buffer_.front());  
    buffer_.pop_front();  
    co_return result;  
  }  
  
  void push(T arg) {  
    buffer_.push_back(std::move(arg));  
    cv_.cancel_one();  
  }  
  
  auto get_executor() -> executor_type { return ioc_.get_executor(); }  
  
 private:  
  boost::asio::io_context& ioc_;  
  boost::asio::steady_timer cv_;  
  boost::circular_buffer<T> buffer_;  
};  
```  
  
A sample way to start worker and use it can be the following:  
```c++  
      co_spawn(worker_.get_executor(), worker_.run(), net::detached);  
  
      std::thread worker_thr{[&]() {  
        worker_.start();  
      }};  
```  
Then from a the main thread you can just call `worker.write(payload)` with your custom payload (just ensure it can be serialized into JSON using boost::describe and that the topic of the message can be inferred from it.  
```c++  
  /* Just create a struct with a couple of basic fields and a function to get the topic name the topic name can be  
   * anything you want it to be. It is not important.  
   */  
  
  template <typename Event>  
  auto write(Event&& event) {  
    net::post(worker_pool_, [this, evt = std::forward<Event>(event)]() {  
      std::string msg{  
          boost::json::serialize(boost::json::value_from(evt.value_))};  
      write_to_redis(evt.get_topic_name(), msg);  
      write_to_log(msg);  
    });  
```  
I understand that most likely the issue is on my side due to improper error handling or cancellation however, this has been a pretty nasty bug that I could not figure out for the greater part of 4 days time. Thank you in advance and if you have any questions let me know.  
After further investigation another very weir behavior was observer:   
```c++  
auto worker::redis_writer() -> net::awaitable<void> {  
  auto cans = co_await coro::cancellation_state;  
  
  net::steady_timer timer{ctx_};  
  timer.expires_after(2s);  
  sys::error_code erc;  
  co_await timer.async_wait(  
      net::bind_executor(ctx_, net::redirect_error(net::deferred, erc)));  
  
  if (erc) {  
    TR_WARN(LOG_NAME, "(redis_writes) first wait error: {0}", erc.what());  
    co_return;  
  }  
  
  while (!cans.cancelled()) {  
    TR_INFO(LOG_NAME, "(redis_writer) waiting to pop...");  
    auto res = co_await redis_message_buf_.pop();  
    redis::request req;  
    req.push("PUBLISH", res.topic, res.msg);  
    TR_WARN(LOG_NAME, "(redis_write) publishing: {0}", req.payload());  
    co_await redis_conn_->async_exec(req, redis::ignore,  
                                     net::redirect_error(net::deferred, erc));  
    if (erc) {  
      TR_ERROR(LOG_NAME, "(redis_writes) error when publishing: {0}",  
               erc.what());  
      break;  
    }  
  }  
}  
```  
I have added the timer and tried to wait for it. However, the timer gets an error that it has been cancelled. This is particularly interesting since the timer is local variable and is not passed anywhere else to be cancelled. After debugging I also notice that the io_context is indeed running so it is not because it was stopped? I have not explanation of why that is the case.
