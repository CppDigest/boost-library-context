# #37 - start doesn't get called on a submachine, nor gets called on_entry [Closed]

> Username: AndrewBloom  
> Created at: 2021-03-08 13:55:11 UTC  
> Updated at: 2024-02-09 07:15:18 UTC  
> Closed at: 2024-02-09 07:15:17 UTC  
> Url: https://github.com/boostorg/msm/issues/37  

```c++  
// Pick a back-end  
class DiscoverySM : public msm::back::state_machine<DiscoverySMFE> {  
private:  
    std::mutex mtx; // prevent race conditions on ctx and signals when SM is stopped and restarted  
public:  
    DiscoverySM() = default;  
    DiscoverySM(ThreadedSM* parentSM) : msm::back::state_machine<DiscoverySMFE>(parentSM) { };  
    // copy constructor doesn't copy the mutex (which is uncopyable and unmovable)  
    DiscoverySM(const DiscoverySM& other) : msm::back::state_machine<DiscoverySMFE>(other) { };  
    DiscoverySM& operator=(const DiscoverySM& other) { msm::back::state_machine<DiscoverySMFE>::operator=(other); return *this; };  
  
    void start() {  
        std::cout << "start called!" << std::endl;  
        std::thread([&]() {  
            std::unique_lock<std::mutex> lck(mtx);  
            ctx = new boost::asio::io_service(1);  
            signals = new boost::asio::signal_set(*ctx, SIGINT, SIGTERM);  
            signals->async_wait([&](const boost::system::error_code& ec, int signum) {  
                if (!ec) ctx->stop();  
                });  
            co_spawn(*ctx, [&]()->awaitable<void> {msm::back::state_machine<DiscoverySMFE>::start(); co_return; }, detached);  
            ctx->run();  
            delete signals;  
            delete ctx;  
            std::cout << "DiscoverySM end thread" << std::endl;  
            }).detach();  
    };  
    void stop() {  
            co_spawn(*ctx, [&]()->awaitable<void> {msm::back::state_machine<DiscoverySMFE>::stop(); co_return; }, detached);  
    }  
    template <class Event, class FSM>  
    void on_entry(Event const&, FSM& fsm) {  
        std::cout << "DiscoverySM onEntry" << std::endl;  
        start();  
    }  
    template <class Event, class FSM>  
    void on_exit(Event const&, FSM&) {  
        //stop();  
    }  
};  
```   
This is a submachine, i can't intercept its entry point. The most natural way should be to override the start method, but it's not get called and my io_context in null. Looking at the source code i thought it would call an on_entry method as all the other states (being a submachine a state), but it doesn't work.

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-02-09 07:15:17 UTC  
> Url: https://github.com/boostorg/msm/issues/37#issuecomment-1935438460  

overwriting a non virtual member is unlikely to go well.  
Your on_entry / on_exit solution could work but only if they were defined in the front-end, not the back end-derived class.
