# #57 - Safe disconnection [Closed]

> Username: ruszkait  
> Created at: 2021-12-28 10:45:19 UTC  
> Updated at: 2022-01-05 07:10:23 UTC  
> Closed at: 2022-01-05 07:10:22 UTC  
> Url: https://github.com/boostorg/signals2/issues/57  

As the documentation states: when I disconnect a slot, then there is a chance that an emission is running from another thread. This makes me some trouble with my application, because after disconnection I cannot be sure that my callback object is really not used any more.  
  
I did not find (or overlooked) any solution in signals2. So I came up with a special scoped_connection, where I inject/capture a RAII tracker object into the slot (see the template constructor PostEmissionSafeConnection), so that in the connection I know when all slot copies are gone. The RAII tracker object (here: HandlerTrace) manipulates an atomic reference counter (TraceCounter) to keep track of the slot copies. By the way it was interesting to see how many copies of the slot object is created during registration (via the slot counter) - maybe that can be optimized too?  
  
I wonder if there is a better solution to that or this implementation sketch can evolve into the signals2 library. I used here C++20, but it can be relaxed.  
  
What do you think about this idea?  
  
```C++  
#include <latch>  
#include <future>  
#include <thread>  
#include <chrono>  
#include <cassert>  
  
class PostEmissionSafeConnection : public boost::signals2::scoped_connection  
{  
public:  
    template <typename TSignal, typename THandler>  
    PostEmissionSafeConnection(TSignal &signal, const THandler &handler)  
        : counter_(std::make_unique<TraceCounter>())  
    {  
        auto trackedHandler = [handlerTrace = HandlerTrace(*(counter_.get())), handler] { handler(); };  
        boost::signals2::scoped_connection::operator=(signal.connect(std::move(trackedHandler)));  
    }  
  
    PostEmissionSafeConnection(const PostEmissionSafeConnection &) = delete;  
    PostEmissionSafeConnection &operator=(const PostEmissionSafeConnection &) = delete;  
  
    PostEmissionSafeConnection(PostEmissionSafeConnection &&other)  
        : boost::signals2::scoped_connection::scoped_connection(std::move(other))  
        , counter_(std::move(other.counter_))  
    { }  
  
    PostEmissionSafeConnection &operator=(PostEmissionSafeConnection &&other)  
    {  
        Release();  
  
        boost::signals2::scoped_connection::operator=(std::move(other));  
        counter_ = std::move(other.counter_);  
  
        return *this;  
    }  
  
    ~PostEmissionSafeConnection() { Release(); }  
  
    // The base class methods can be used  
    // disconnect(), connected(), blocked() etc.  
    // The only thing is: this class blocks at the destructor until all handlers are released  
  
private:  
    // A shared counter, which tracks the living Trace instances  
    // NOTE: This object is on the heap. This pins down its address, so the Trace-es can refer to it via  
    // a C++ reference. The Connection can be moved, because that also refers to this fixed address counter.  
    struct TraceCounter  
    {  
        void RegisterCreation()  
        {  
            auto currentTraceCount = traceCount_.load();  
            assert(traceCount_ == 0);  
            for (;;)  
            {  
                auto incrementedTraceCount = currentTraceCount + 1;  
  
                const bool exchanged = traceCount_.compare_exchange_strong(  
                    currentTraceCount, incrementedTraceCount, std::memory_order_release, std::memory_order_relaxed);  
  
                if (exchanged)  
                    break;  
            }  
        }  
  
        void RegisterCopy()  
        {  
            auto currentTraceCount = traceCount_.load();  
            assert(currentTraceCount > 0);  
            for (;;)  
            {  
                auto incrementedTraceCount = currentTraceCount + 1;  
  
                const bool exchanged = traceCount_.compare_exchange_strong(  
                    currentTraceCount, incrementedTraceCount, std::memory_order_release, std::memory_order_relaxed);  
  
                if (exchanged)  
                    break;  
            }  
        }  
  
        void UnRegister()  
        {  
            auto currentTraceCount = traceCount_.load();  
            assert(currentTraceCount > 0);  
            for (;;)  
            {  
                auto decrementedTraceCount = currentTraceCount - 1;  
  
                const bool exchanged = traceCount_.compare_exchange_strong(  
                    currentTraceCount, decrementedTraceCount, std::memory_order_release, std::memory_order_relaxed);  
  
                if (exchanged)  
                    break;  
            }  
        }  
  
        void WaitForLastHandlerTrace()  
        {  
            for (;;)  
            {  
                auto currentTraceCount = traceCount_.load();  
  
                if (currentTraceCount == 0)  
                    break;  
  
                using namespace std::literals;  
                std::this_thread::sleep_for(1ms);  
            }  
        }  
  
        std::atomic<std::size_t> traceCount_ = 0;  
    };  
  
    // RAII object captured in the handler, so that we can keep track of the living handlers  
    struct HandlerTrace  
    {  
        HandlerTrace() = delete;  
  
        HandlerTrace(TraceCounter &counter)  
            : counter_(&counter)  
        {  
            counter_->RegisterCreation();  
        }  
  
        HandlerTrace(const HandlerTrace &other)  
            : counter_(other.counter_)  
        {  
            counter_->RegisterCopy();  
        }  
  
        HandlerTrace &operator=(const HandlerTrace &other)  
        {  
            counter_ = other.counter_;  
            counter_->RegisterCopy();  
            return *this;  
        }  
  
        HandlerTrace(HandlerTrace &&other)  
            : counter_(other.counter_)  
        {  
            other.counter_ = nullptr;  
        }  
  
        HandlerTrace &operator=(HandlerTrace &&other)  
        {  
            counter_ = other.counter_;  
            other.counter_ = nullptr;  
            return *this;  
        }  
  
        ~HandlerTrace()  
        {  
            if (counter_)  
                counter_->UnRegister();  
        }  
  
        TraceCounter *counter_ = nullptr;  
    };  
  
    void Release()  
    {  
        if (!counter_)  
            return;  
  
        disconnect();  
        counter_->WaitForLastHandlerTrace();  
        counter_.reset();  
    }  
  
    std::unique_ptr<TraceCounter> counter_;  
};  
  
TEST(SignalTest, PostUnsubscriptionEmission)  
{  
    // The handler must outlive all traces  
    boost::signals2::signal<void()> signal;  
  
    std::latch latch(2);  
    PostEmissionSafeConnection connection(signal, [&latch] {  
        latch.count_down();  
        using namespace std::literals;  
        std::this_thread::sleep_for(2s);  
        std::this_thread::sleep_for(2s);  
    });  
  
    std::thread signalSenderThread([&signal] { signal(); });  
    signalSenderThread.detach();  
  
    latch.arrive_and_wait();  
    connection.disconnect();  
  
    // the connection blocks its destruction until all handler instances are destroyed.  
}  
  
```

---

## Comment 1

> Username: fmhess  
> Created at: 2021-12-28 22:11:54 UTC  
> Url: https://github.com/boostorg/signals2/issues/57#issuecomment-1002301720  

It would probably be simpler to just add a tracked shared_ptr when you  
connect your slot.  The shared_ptr will call its deleter when all signal  
invocations using the slot are done (and you have released your copy of the  
shared_ptr).  
  
On Tue, Dec 28, 2021, 05:45 ruszkait ***@***.***> wrote:  
  
> As the documentation states: when I disconnect a slot, then there is a  
> chance that an emission is running from another thread. This makes me some  
> trouble with my application, because after disconnection I cannot be sure  
> that my callback object is really not used any more.  
>  
> I did not find (or overlooked) any solution in signals2. So I came up with  
> a special scoped_connection, where I inject/capture a RAII tracker object  
> into the slot (see the template constructor PostEmissionSafeConnection), so  
> that in the connection I know when all slot copies are gone. The RAII  
> tracker object (here: HandlerTrace) manipulates an atomic reference counter  
> (TraceCounter) to keep track of the slot copies. By the way it was  
> interesting to see how many copies of the slot object is created during  
> registration (via the slot counter) - maybe that can be optimized too?  
>  
> I wonder if there is a better solution to that or this implementation  
> sketch can evolve into the signals2 library. I used here C++20, but it can  
> be relaxed.  
>  
> What do you think about this idea?  
>  
> #include <latch>  
> #include <future>  
> #include <thread>  
> #include <chrono>  
> #include <cassert>  
>  
> class PostEmissionSafeConnection : public boost::signals2::scoped_connection  
> {  
> public:  
>     template <typename TSignal, typename THandler>  
>     PostEmissionSafeConnection(TSignal &signal, const THandler &handler)  
>         : counter_(std::make_unique<TraceCounter>())  
>     {  
>         auto trackedHandler = [handlerTrace = HandlerTrace(*(counter_.get())), handler] { handler(); };  
>         boost::signals2::scoped_connection::operator=(signal.connect(std::move(trackedHandler)));  
>     }  
>  
>     PostEmissionSafeConnection(const PostEmissionSafeConnection &) = delete;  
>     PostEmissionSafeConnection &operator=(const PostEmissionSafeConnection &) = delete;  
>  
>     PostEmissionSafeConnection(PostEmissionSafeConnection &&other)  
>         : boost::signals2::scoped_connection::scoped_connection(std::move(other))  
>         , counter_(std::move(other.counter_))  
>     { }  
>  
>     PostEmissionSafeConnection &operator=(PostEmissionSafeConnection &&other)  
>     {  
>         Release();  
>  
>         boost::signals2::scoped_connection::operator=(std::move(other));  
>         counter_ = std::move(other.counter_);  
>  
>         return *this;  
>     }  
>  
>     ~PostEmissionSafeConnection() { Release(); }  
>  
>     // The base class methods can be used  
>     // disconnect(), connected(), blocked() etc.  
>     // The only thing is: this class blocks at the destructor until all handlers are released  
>  
> private:  
>     // A shared counter, which tracks the living Trace instances  
>     // NOTE: This object is on the heap. This pins down its address, so the Trace-es can refer to it via  
>     // a C++ reference. The Connection can be moved, because that also refers to this fixed address counter.  
>     struct TraceCounter  
>     {  
>         void RegisterCreation()  
>         {  
>             auto currentTraceCount = traceCount_.load();  
>             assert(traceCount_ == 0);  
>             for (;;)  
>             {  
>                 auto incrementedTraceCount = currentTraceCount + 1;  
>  
>                 const bool exchanged = traceCount_.compare_exchange_strong(  
>                     currentTraceCount, incrementedTraceCount, std::memory_order_release, std::memory_order_relaxed);  
>  
>                 if (exchanged)  
>                     break;  
>             }  
>         }  
>  
>         void RegisterCopy()  
>         {  
>             auto currentTraceCount = traceCount_.load();  
>             assert(currentTraceCount > 0);  
>             for (;;)  
>             {  
>                 auto incrementedTraceCount = currentTraceCount + 1;  
>  
>                 const bool exchanged = traceCount_.compare_exchange_strong(  
>                     currentTraceCount, incrementedTraceCount, std::memory_order_release, std::memory_order_relaxed);  
>  
>                 if (exchanged)  
>                     break;  
>             }  
>         }  
>  
>         void UnRegister()  
>         {  
>             auto currentTraceCount = traceCount_.load();  
>             assert(currentTraceCount > 0);  
>             for (;;)  
>             {  
>                 auto decrementedTraceCount = currentTraceCount - 1;  
>  
>                 const bool exchanged = traceCount_.compare_exchange_strong(  
>                     currentTraceCount, decrementedTraceCount, std::memory_order_release, std::memory_order_relaxed);  
>  
>                 if (exchanged)  
>                     break;  
>             }  
>         }  
>  
>         void WaitForLastHandlerTrace()  
>         {  
>             for (;;)  
>             {  
>                 auto currentTraceCount = traceCount_.load();  
>  
>                 if (currentTraceCount == 0)  
>                     break;  
>  
>                 using namespace std::literals;  
>                 std::this_thread::sleep_for(1ms);  
>             }  
>         }  
>  
>         std::atomic<std::size_t> traceCount_ = 0;  
>     };  
>  
>     // RAII object captured in the handler, so that we can keep track of the living handlers  
>     struct HandlerTrace  
>     {  
>         HandlerTrace() = delete;  
>  
>         HandlerTrace(TraceCounter &counter)  
>             : counter_(&counter)  
>         {  
>             counter_->RegisterCreation();  
>         }  
>  
>         HandlerTrace(const HandlerTrace &other)  
>             : counter_(other.counter_)  
>         {  
>             counter_->RegisterCopy();  
>         }  
>  
>         HandlerTrace &operator=(const HandlerTrace &other)  
>         {  
>             counter_ = other.counter_;  
>             counter_->RegisterCopy();  
>             return *this;  
>         }  
>  
>         HandlerTrace(HandlerTrace &&other)  
>             : counter_(other.counter_)  
>         {  
>             other.counter_ = nullptr;  
>         }  
>  
>         HandlerTrace &operator=(HandlerTrace &&other)  
>         {  
>             counter_ = other.counter_;  
>             other.counter_ = nullptr;  
>             return *this;  
>         }  
>  
>         ~HandlerTrace()  
>         {  
>             if (counter_)  
>                 counter_->UnRegister();  
>         }  
>  
>         TraceCounter *counter_ = nullptr;  
>     };  
>  
>     void Release()  
>     {  
>         if (!counter_)  
>             return;  
>  
>         disconnect();  
>         counter_->WaitForLastHandlerTrace();  
>         counter_.reset();  
>     }  
>  
>     std::unique_ptr<TraceCounter> counter_;  
> };  
>  
> TEST(SignalTest, PostUnsubscriptionEmission)  
> {  
>     // The handler must outlive all traces  
>     boost::signals2::signal<void()> signal;  
>  
>     std::latch latch(2);  
>     PostEmissionSafeConnection connection(signal, [&latch] {  
>         latch.count_down();  
>         using namespace std::literals;  
>         std::this_thread::sleep_for(2s);  
>         std::this_thread::sleep_for(2s);  
>     });  
>  
>     std::thread signalSenderThread([&signal] { signal(); });  
>     signalSenderThread.detach();  
>  
>     latch.arrive_and_wait();  
>     connection.disconnect();  
>  
>     // the connection blocks its destruction until all handler instances are destroyed.  
> }  
>  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/signals2/issues/57>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AARKTX5X3IUMS4WDDSWXZUDUTGIMTANCNFSM5K35EOWQ>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 2

> Username: ruszkait  
> Created at: 2021-12-29 19:57:54 UTC  
> Updated at: 2021-12-29 19:58:43 UTC  
> Url: https://github.com/boostorg/signals2/issues/57#issuecomment-1002754834  

Thanks! I did not know that a shared_ptr can be of type void. This simplifies things and still I need only one heap allocation for the control block. In the destructor I use polling the tracker instead of future to avoid another allocation on the heap.  
  
I have made an example how I like to encapsulate the signal behind a subscription method (see class BatteryController). This seems to work fine now. I can even specify in my subscription method if I want to have post unsubscription callback protection or not. I would like to enable the user to decide if he wants to have tracking or not. Tracking is not always useful and can lead to deadlock when I unsubscribe from an emission.  
  
This new subscription is just one weak_ptr more (size of two raw pointer according to cppreference.com), than the scoped_connection.  
  
Would it be worth to put this into the library (of course with a better name and with clean-up)?  
  
```cpp  
#include <latch>  
#include <future>  
#include <thread>  
#include <chrono>  
#include <cassert>  
  
class PostEmissionSafeConnection : public boost::signals2::scoped_connection  
{  
public:  
    PostEmissionSafeConnection(boost::signals2::connection connection, std::weak_ptr<void> tracker)  
        : boost::signals2::scoped_connection(connection)  
        , tracker_(tracker)  
    { }  
  
    PostEmissionSafeConnection(const PostEmissionSafeConnection &) = delete;  
    PostEmissionSafeConnection &operator=(const PostEmissionSafeConnection &) = delete;  
  
    PostEmissionSafeConnection(PostEmissionSafeConnection &&other) = default;  
  
    PostEmissionSafeConnection &operator=(PostEmissionSafeConnection &&other)  
    {  
        Release();  
  
        boost::signals2::scoped_connection::operator=(std::move(other));  
        tracker_ = std::move(other.tracker_);  
  
        return *this;  
    }  
  
    ~PostEmissionSafeConnection() { Release(); }  
  
    // The base class methods can be used  
    // disconnect(), connected(), blocked() etc.  
    // The only thing is: this class blocks at the destructor until all handlers are released  
  
private:  
    void Release()  
    {  
        while (!tracker_.expired())  
        {  
            using namespace std::literals;  
            std::this_thread::sleep_for(1ms);  
        }  
    }  
  
    std::weak_ptr<void> tracker_;  
};  
  
class BatteryController  
{  
public:  
    PostEmissionSafeConnection OnBatteryLow(const std::function<void()> &handler, bool emissionSafe = false)  
    {  
        std::weak_ptr<void> weakTracker;  
        boost::signals2::connection connection;  
  
        if (!emissionSafe)  
        {  
            connection = batteryLowSignal_.connect(handler);  
        }  
        else  
        {  
            std::shared_ptr<void> tracker(nullptr, [](auto) {});  
            weakTracker = tracker;  
            auto trackedHandler = [tracker = std::move(tracker), handler] { handler(); };  
            connection = batteryLowSignal_.connect(std::move(trackedHandler));  
        }  
  
        PostEmissionSafeConnection safeConnection(connection, weakTracker);  
        return safeConnection;  
    }  
  
    void NotifyBatteryLow() { batteryLowSignal_(); }  
  
private:  
    boost::signals2::signal<void()> batteryLowSignal_;  
};  
  
TEST(SignalTest, PostUnsubscriptionEmission)  
{  
    BatteryController batteryController;  
  
    std::latch latch(2);  
    PostEmissionSafeConnection connection = batteryController.OnBatteryLow([&latch] {  
        latch.count_down();  
        using namespace std::literals;  
        std::this_thread::sleep_for(2s);  
        std::this_thread::sleep_for(2s);  
    }, true);  
  
    std::thread signalSenderThread([&batteryController] { batteryController.NotifyBatteryLow(); });  
    signalSenderThread.detach();  
  
    latch.arrive_and_wait();  
    // At this point we wait for a running emission.  
  
    // disconnect during emission  
    connection.disconnect();  
  
    // the connection blocks in its destruction until all handler instances are destroyed.  
}  
```

---

## Comment 3

> Username: ruszkait  
> Created at: 2021-12-31 08:42:49 UTC  
> Updated at: 2021-12-31 18:38:13 UTC  
> Url: https://github.com/boostorg/signals2/issues/57#issuecomment-1003310656  

Here is another variant without polling.  
  
```cpp  
struct TraceCounter  
{  
    void RegisterTrace()  
    {  
        std::lock_guard lock(mutex_);  
        traceCount_++;  
    }  
  
    void UnRegisterTrace()  
    {  
        std::lock_guard lock(mutex_);  
        traceCount_--;  
        if (traceCount_ == 0)  
            traceCountReached0Condition_.notify_one();  
    }  
  
    void WaitForLastHandlerTrace()  
    {  
        std::unique_lock lock(mutex_);  
        traceCountReached0Condition_.wait(lock, [this] { return traceCount_ == 0; });  
    }  
  
    std::mutex mutex_;  
    std::condition_variable traceCountReached0Condition_;  
    std::size_t traceCount_ = 0;  
};  
  
class PostEmissionSafeConnection : public boost::signals2::scoped_connection  
{  
public:  
    PostEmissionSafeConnection() { }  
  
    PostEmissionSafeConnection(  
        boost::signals2::connection &&connection, std::unique_ptr<TraceCounter> counter = nullptr)  
        : scoped_connection(std::move(connection))  
        , counter_(std::move(counter))  
    { }  
  
    PostEmissionSafeConnection(const PostEmissionSafeConnection &) = delete;  
    PostEmissionSafeConnection &operator=(const PostEmissionSafeConnection &) = delete;  
  
    PostEmissionSafeConnection(PostEmissionSafeConnection &&other)  
        : scoped_connection(std::move(other))  
        , counter_(std::move(other.counter_))  
    { }  
  
    PostEmissionSafeConnection &operator=(PostEmissionSafeConnection &&other)  
    {  
        Release();  
  
        boost::signals2::scoped_connection::operator=(std::move(other));  
        counter_ = std::move(other.counter_);  
  
        return *this;  
    }  
  
    ~PostEmissionSafeConnection() { Release(); }  
  
    // The base class methods can be used  
    // disconnect(), connected(), blocked() etc.  
    // The only thing is: this class blocks at the destructor until all handlers are released  
  
private:  
    void Release()  
    {  
        if (!counter_)  
            return;  
  
        counter_->WaitForLastHandlerTrace();  
        counter_.reset();  
    }  
  
    std::unique_ptr<TraceCounter> counter_;  
};  
  
struct Tracer  
{  
    Tracer() = delete;  
  
    Tracer(TraceCounter *counter)  
        : counter_(counter)  
    {  
        counter_->RegisterTrace();  
    }  
  
    Tracer(const Tracer &other)  
        : counter_(other.counter_)  
    {  
        counter_->RegisterTrace();  
    }  
  
    Tracer &operator=(const Tracer &other)  
    {  
        counter_ = other.counter_;  
        counter_->RegisterTrace();  
        return *this;  
    }  
  
    Tracer(Tracer &&other)  
        : counter_(other.counter_)  
    {  
        other.counter_ = nullptr;  
    }  
  
    Tracer &operator=(Tracer &&other)  
    {  
        counter_ = other.counter_;  
        other.counter_ = nullptr;  
        return *this;  
    }  
  
    ~Tracer()  
    {  
        if (counter_)  
            counter_->UnRegisterTrace();  
    }  
  
    TraceCounter *counter_;  
};  
  
class BatteryController  
{  
public:  
    PostEmissionSafeConnection OnBatteryLow(const std::function<void()> &handler, bool emissionSafe = false)  
    {  
        if (!emissionSafe)  
        {  
            return batteryLowSignal_.connect(handler);  
        }  
  
        auto counter = std::make_unique<TraceCounter>();  
        auto connection = batteryLowSignal_.connect([tracer = Tracer(counter.get()), handler] { handler(); });  
        return {std::move(connection), std::move(counter)};  
    }  
  
    void NotifyBatteryLow() { batteryLowSignal_(); }  
  
private:  
    boost::signals2::signal<void()> batteryLowSignal_;  
};  
  
TEST(SignalTest, PostUnsubscriptionEmission)  
{  
    BatteryController batteryController;  
  
    std::latch latch(2);  
    PostEmissionSafeConnection connection = batteryController.OnBatteryLow(  
        [&latch] {  
            latch.count_down();  
            using namespace std::literals;  
            std::this_thread::sleep_for(2s);  
            std::this_thread::sleep_for(2s);  
        },  
        true);  
  
    std::thread signalSenderThread([&batteryController] { batteryController.NotifyBatteryLow(); });  
    signalSenderThread.detach();  
  
    latch.arrive_and_wait();  
    // At this point we wait for a running emission.  
  
    // disconnect during emission  
    connection.disconnect();  
  
    // the connection blocks in its destruction until all handler instances are destroyed.  
}  
```

---

## Comment 4

> Username: fmhess  
> Created at: 2022-01-04 16:46:41 UTC  
> Url: https://github.com/boostorg/signals2/issues/57#issuecomment-1004983553  

Hi, I don't want to extend the library interface at this point, unless it addresses some critical flaw.  Your code might be interesting for a programming blog though.

---

## Comment 5

> Username: ruszkait  
> Created at: 2022-01-05 07:10:22 UTC  
> Url: https://github.com/boostorg/signals2/issues/57#issuecomment-1005435501  

Ok, I understand that. So then I am going to close the issue.
