# #318 - use it in qt [Open]

> Username: bizehao  
> Created at: 2024-04-07 10:32:30 UTC  
> Updated at: 2024-04-07 10:32:30 UTC  
> Url: https://github.com/boostorg/fiber/issues/318  

This is the scheduling I implemented in QT, but it has issues and sometimes it won't be scheduled. Occasionally, I am confused and can't find where the problem lies  
  
```  
using namespace std::chrono_literals;  
namespace {  
	uint64_t milliseconds_until(  
		const std::chrono::steady_clock::time_point& abs_time)  
	{  
		using std::chrono::duration_cast;  
		auto now = std::chrono::steady_clock::now();  
		if (now >= abs_time) return 0;  
		return duration_cast<std::chrono::milliseconds>(abs_time - now).count() + 1;  
	}  
	bool is_max_time(const std::chrono::steady_clock::time_point& abs_time)  
	{  
		return abs_time == std::chrono::steady_clock::time_point::max();  
	}  
}  
  
class scheduler : public boost::fibers::algo::algorithm  
{  
public:  
	scheduler() {  
		QTimer::singleShot(0, [this]() mutable {  
			QEventLoop eventLoop;  
			while (true) {  
				if (has_ready_fibers()) {  
					//eventLoop.processEvents(QEventLoop::ApplicationExec | QEventLoop::EventLoopExec);  
					while (eventLoop.processEvents(QEventLoop::AllEvents));  
					std::unique_lock< boost::fibers::mutex > lk(mtx_);  
					cnd_.wait(lk);  
				}  
				else {  
					eventLoop.processEvents(QEventLoop::WaitForMoreEvents);  
				}  
			}  
			});  
	};  
  
	~scheduler() override {};  
  
	void awakened(boost::fibers::context* ctx) noexcept override {  
		BOOST_ASSERT(nullptr != ctx);  
		BOOST_ASSERT(!ctx->ready_is_linked());  
		ctx->ready_link(rqueue_); /*< fiber, enqueue on ready queue >*/  
		if (!ctx->is_context(boost::fibers::type::dispatcher_context)) {  
			++counter_;  
		}  
	}  
  
	boost::fibers::context* pick_next() noexcept override {  
		boost::fibers::context* ctx = nullptr;  
		if (!rqueue_.empty()) {  
			ctx = &rqueue_.front();  
			rqueue_.pop_front();  
			BOOST_ASSERT(nullptr != ctx);  
			BOOST_ASSERT(boost::fibers::context::active() != ctx);  
			if (!ctx->is_context(boost::fibers::type::dispatcher_context)) {  
				--counter_;  
			}  
		}  
		return ctx;  
	}  
  
	bool has_ready_fibers() const noexcept override {  
		return 0 < counter_;  
	}  
  
	void suspend_until(std::chrono::steady_clock::time_point const& abs_time) noexcept override {  
		if (!is_max_time(abs_time)) {  
			QTimer::singleShot(milliseconds_until(abs_time), []() {  
				boost::this_fiber::yield();  
				});  
		}  
		cnd_.notify_one();  
	}  
  
	void notify() noexcept override {  
		QTimer::singleShot(0, []() {  
			boost::this_fiber::yield();  
			});  
	}  
  
	// Non-copyable and non-movable  
	scheduler(const scheduler&) = delete;  
	scheduler& operator=(const scheduler&) = delete;  
	scheduler(scheduler&&) = delete;  
	scheduler& operator=(scheduler&&) = delete;  
  
private:  
	boost::fibers::mutex                            mtx_;  
	boost::fibers::condition_variable               cnd_;  
	std::size_t                                     counter_{ 0 };  
	boost::fibers::scheduler::ready_queue_type      rqueue_;  
};  
  
//test code  
boost::fibers::use_scheduling_algorithm<scheduler>();  
  
boost::fibers::fiber ff{ []() {  
		for (int i = 0; i < 10; i++) {  
			boost::this_fiber::sleep_for(5s);  
			qDebug() << "i: " << i;  
		}  
		} };  
  
ff.detach();  
```
