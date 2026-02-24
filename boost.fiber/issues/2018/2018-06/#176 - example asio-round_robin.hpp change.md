# #176 - example asio/round_robin.hpp change [Closed]

> Username: jxfwinter  
> Created at: 2018-06-16 06:49:57 UTC  
> Updated at: 2018-06-19 00:50:08 UTC  
> Closed at: 2018-06-19 00:50:08 UTC  
> Url: https://github.com/boostorg/fiber/issues/176  

```  
namespace boost {  
namespace fibers {  
namespace asio {  
  
class round_robin : public algo::algorithm {  
private:  
    std::shared_ptr< boost::asio::io_service >      io_svc_;  
    boost::fibers::scheduler::ready_queue_type      rqueue_{};  
  
public:  
    struct service : public boost::asio::io_service::service {  
        static boost::asio::io_service::id                  id;  
        std::unique_ptr< boost::asio::io_service::work >    work_;  
  
        service( boost::asio::io_service & io_svc) :  
            boost::asio::io_service::service( io_svc),  
            work_{ new boost::asio::io_service::work( io_svc) } {  
        }  
  
        virtual ~service() {}  
  
        service( service const&) = delete;  
        service & operator=( service const&) = delete;  
  
        void shutdown_service() override final {  
            work_.reset();  
        }  
    };  
  
    round_robin( std::shared_ptr< boost::asio::io_service > const& io_svc) :  
        io_svc_( io_svc)  {  
        boost::asio::add_service( * io_svc_, new service( * io_svc_) );  
        io_svc_->post([this]() mutable {  
                while ( ! io_svc_->stopped() ) {  
                    //this is not gracefully, but can work  
                    boost::this_fiber::sleep_for(std::chrono::milliseconds(50));  
               }  
            });  
    }  
  
    void awakened( context * ctx) noexcept {  
        BOOST_ASSERT( nullptr != ctx);  
        BOOST_ASSERT( ! ctx->ready_is_linked() );  
        ctx->ready_link( rqueue_);  
    }  
  
    context * pick_next() noexcept {  
        context * ctx( nullptr);  
        if ( ! rqueue_.empty() )  {   
            ctx = & rqueue_.front();  
            rqueue_.pop_front();  
            BOOST_ASSERT( nullptr != ctx);  
            BOOST_ASSERT( context::active() != ctx);  
        }  
        return ctx;  
    }  
  
    bool has_ready_fibers() const noexcept {  
        return ! rqueue_.empty();  
    }  
  
    void suspend_until( std::chrono::steady_clock::time_point const& abs_time) noexcept {  
        while ( io_svc_->poll() );  
        io_svc_->run_one_until(abs_time);  
    }  
  
    void notify() noexcept {  
         io_svc_->post([]() mutable {  
         });  
    }  
};  
  
boost::asio::io_service::id round_robin::service::id;  
  
}}}  
```  
  
I changed fiber\examples\asio\round_robin.hpp, I test success in my case, but I do not know whether cause other problem,  
can someone  check it?

---

## Comment 1

> Username: olk  
> Created at: 2018-06-18 12:18:53 UTC  
> Updated at: 2018-06-18 12:20:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/176#issuecomment-398035091  

I think it would not process non-asio related code (e.g. fibers not dealing with asio).   
The scheduler algorithm is required to integrate with asio's io_context, e.g fibers using and not using asio must be processed as well as io_context must be able to handle non-fiber code.

---

## Comment 2

> Username: jxfwinter  
> Created at: 2018-06-19 00:50:08 UTC  
> Url: https://github.com/boostorg/fiber/issues/176#issuecomment-398239686  

ty
