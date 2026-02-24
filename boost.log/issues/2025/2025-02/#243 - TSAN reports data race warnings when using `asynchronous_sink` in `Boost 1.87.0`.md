# #243 - TSAN reports data race warnings when using `asynchronous_sink` in `Boost 1.87.0` [Closed]

> Username: 3manifold  
> Created at: 2025-02-13 11:12:18 UTC  
> Updated at: 2025-02-22 14:09:15 UTC  
> Closed at: 2025-02-22 14:09:14 UTC  
> Url: https://github.com/boostorg/log/issues/243  

TSAN still reports warnings when using `asynchronous_sink` in `Boost 1.87.0`. According to https://github.com/boostorg/log/commit/a75c4f45c4560e4bbcee2f4a77d36eac48675171 , the issue should be fixed in `Boost 1.87.0`.  
  
related links: https://github.com/boostorg/log/issues/139 , https://github.com/boostorg/log/commit/a75c4f45c4560e4bbcee2f4a77d36eac48675171

---

## Comment 1

> Username: Lastique  
> Created at: 2025-02-13 13:44:12 UTC  
> Updated at: 2025-02-13 13:45:55 UTC  
> Url: https://github.com/boostorg/log/issues/243#issuecomment-2656644203  

Could you provide the error log and a short reproducer?

---

## Comment 2

> Username: 3manifold  
> Created at: 2025-02-13 13:49:23 UTC  
> Updated at: 2025-02-14 08:46:44 UTC  
> Url: https://github.com/boostorg/log/issues/243#issuecomment-2656657392  

I don't have it in hand but it's more or less what was mentioned earlier. I was expecting that boost was also testing against ASAN/TSAN given the library's popularity, complexity & size.   
  
```cpp  
// reproduce   
#include <boost/core/null_deleter.hpp>  
#include <boost/log/common.hpp>  
#include <boost/log/core.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/sinks.hpp>  
#include <boost/log/sinks/sync_frontend.hpp>  
#include <boost/log/sinks/text_ostream_backend.hpp>  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/sources/record_ostream.hpp>  
#include <boost/log/sources/severity_logger.hpp>  
#include <boost/log/trivial.hpp>  
#include <boost/log/utility/setup/common_attributes.hpp>  
#include <boost/log/utility/setup/common_attributes.hpp>  
#include <boost/smart_ptr/make_shared_object.hpp>  
#include <boost/smart_ptr/shared_ptr.hpp>   
  
#include <fstream>  
#include <iostream>  
  
namespace logging = boost::log;  
namespace src = boost::log::sources;  
namespace expr = boost::log::expressions;  
using namespace boost::log;  
  
int main() {  
    auto sink = boost::make_shared<sinks::asynchronous_sink<sinks::text_ostream_backend>>();  
    sink->locked_backend()->add_stream({&std::clog, boost::null_deleter{}});  
    sink->set_formatter(expr::format("%1%: <%2%> %3%") % expr::attr<attributes::current_thread_id::value_type>("ThreadID") %  
                        logging::trivial::severity % expr::smessage);  
  
    logging::core::get()->add_sink(sink);  
    logging::add_common_attributes();  
  
    using namespace logging::trivial;  
    src::severity_logger<severity_level> lg;  
  
    BOOST_LOG_SEV(lg, warning) << "A trace severity message";  
    std::cout << std::endl <<boost::this_thread::get_id() << std::endl;  
    return 0;  
}  
```

---

## Comment 3

> Username: 3manifold  
> Created at: 2025-02-21 20:56:27 UTC  
> Url: https://github.com/boostorg/log/issues/243#issuecomment-2675525234  

Any update on that? I modified the last message with code example.

---

## Comment 4

> Username: Lastique  
> Created at: 2025-02-21 21:09:44 UTC  
> Url: https://github.com/boostorg/log/issues/243#issuecomment-2675547468  

No, I haven't tried to reproduce it yet. I did look at the code though, and from what I can see, Boost.Atomic is used to access shared data. If TSAN is not seeing Boost.Atomic as atomic operations then this is a false positive.

---

## Comment 5

> Username: Lastique  
> Created at: 2025-02-22 14:09:14 UTC  
> Url: https://github.com/boostorg/log/issues/243#issuecomment-2676228733  

I managed to reproduce TSAN warnings with gcc 13.3, full report is here:  
  
[report.txt](https://github.com/user-attachments/files/18923095/report.txt)  
  
In short, TSAN is flagging the deletion of attribute values in the `attribute_value_set` in the main thread as a race against the prior use of these attribute values in filters/formatters in the sink threads.  
  
As I suspected, this is a false positive caused by the fact that TSAN does not recognize asm statements (see [here](https://github.com/google/sanitizers/issues/192) and [here](https://github.com/google/sanitizers/issues/1443)) used by Boost.Atomic to implement atomic reference counter in [`record_view`](https://github.com/boostorg/log/blob/6366d73335d298c8c5c7f7bd80f6931fedb0df1b/include/boost/log/core/record_view.hpp#L70-L80). The deletion of the `attribute_value_set` and all the attribute values is guaranteed to happen after and synchronize with the prior reads of these objects in other threads.
