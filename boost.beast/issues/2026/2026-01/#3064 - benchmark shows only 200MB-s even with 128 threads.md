# #3064 - [perf tuning] benchmark shows only 200MB/s even with 128 threads [Open]

> Username: gaowayne  
> Created at: 2026-01-03 04:00:13 UTC  
> Updated at: 2026-01-04 12:55:41 UTC  
> Url: https://github.com/boostorg/beast/issues/3064  

hello expert  
  
I write one example server and benchmark tool, but I see performance is not good. I write similar tcp server can reach 3GB/s with one tcp connection and one thread.  
  
but beast show very low BW.  
  
my server benchmark code  
```  
// -----------------------------------------------------------------------------  
// Boost.Beast Benchmark - Runs Server and Client Together  
//  
// This program starts an async HTTP server and then runs a client benchmark  
// against it, measuring pure HTTP performance.  
// -----------------------------------------------------------------------------  
  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
#include <boost/beast/http.hpp>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
#include <chrono>  
#include <algorithm>  
#include <iomanip>  
#include <mutex>  
#include <condition_variable>  
  
// Include server and client components  
// For simplicity, we'll inline the key components here  
  
namespace asio = boost::asio;  
namespace beast = boost::beast;  
namespace http = beast::http;  
using tcp = asio::ip::tcp;  
  
// Forward declarations - we'll use simplified versions  
class BenchmarkServer;  
class BenchmarkClient;  
  
struct BenchmarkStats  
{  
    std::mutex mutex;  
    int success_count = 0;  
    int failure_count = 0;  
    std::vector<double> latencies_ms;  
    size_t total_bytes = 0;  
    std::chrono::steady_clock::time_point start_time;  
    std::chrono::steady_clock::time_point end_time;  
  
    void record_success(double latency_ms, size_t bytes)  
    {  
        std::lock_guard<std::mutex> lock(mutex);  
        success_count++;  
        latencies_ms.push_back(latency_ms);  
        total_bytes += bytes;  
    }  
  
    void record_failure()  
    {  
        std::lock_guard<std::mutex> lock(mutex);  
        failure_count++;  
    }  
  
    void print_results()  
    {  
        std::lock_guard<std::mutex> lock(mutex);  
        auto duration = std::chrono::duration<double>(  
            end_time - start_time).count();  
  
        std::cout << "\n" << std::string(70, '=') << "\n";  
        std::cout << "Boost.Beast Benchmark Results\n";  
        std::cout << std::string(70, '=') << "\n";  
        std::cout << "Total requests:  " << (success_count + failure_count) << "\n";  
        std::cout << "  Success:      " << success_count << "\n";  
        std::cout << "  Failed:       " << failure_count << "\n";  
        std::cout << "Duration:       " << std::fixed << std::setprecision(2)   
                  << duration << " seconds\n";  
  
        if (success_count > 0 && duration > 0)  
        {  
            double throughput_mbps = (total_bytes * 8.0) / (duration * 1'000'000.0);  
            double ops_per_sec = success_count / duration;  
            std::cout << "Throughput:     " << std::fixed << std::setprecision(2)  
                      << throughput_mbps << " Mbps ("   
                      << (throughput_mbps / 8.0) << " MB/s)\n";  
            std::cout << "Operations/sec: " << ops_per_sec << "\n";  
        }  
  
        if (!latencies_ms.empty())  
        {  
            std::sort(latencies_ms.begin(), latencies_ms.end());  
            size_t n = latencies_ms.size();  
            double sum = 0;  
            for (auto l : latencies_ms) sum += l;  
  
            std::cout << "\nLatency (milliseconds):\n";  
            std::cout << "  Min:          " << std::fixed << std::setprecision(2)  
                      << latencies_ms[0] << "\n";  
            std::cout << "  Max:          " << latencies_ms[n - 1] << "\n";  
            std::cout << "  Average:      " << (sum / n) << "\n";  
            std::cout << "  Median (p50): " << latencies_ms[n / 2] << "\n";  
            if (n > 0)  
            {  
                std::cout << "  p95:          "   
                          << latencies_ms[static_cast<size_t>(n * 0.95)] << "\n";  
                std::cout << "  p99:          "   
                          << latencies_ms[static_cast<size_t>(n * 0.99)] << "\n";  
            }  
        }  
        std::cout << std::string(70, '=') << "\n";  
    }  
};  
  
// Simplified server session (inline version)  
class server_session : public std::enable_shared_from_this<server_session>  
{  
public:  
    explicit server_session(tcp::socket socket)  
        : socket_(std::move(socket))  
        , strand_(socket_.get_executor())  
    {  
        // Configure socket for maximum performance  
        // These settings are critical for achieving high bandwidth (3GB/s target)  
        beast::error_code ec;  
          
        // Disable Nagle's algorithm for low latency  
        socket_.set_option(tcp::no_delay(true), ec);  
          
        // Use large buffers (8MB) to match epolls_benchmark performance  
        // Small default buffers (64KB-256KB) severely limit bandwidth  
        socket_.set_option(asio::socket_base::send_buffer_size(8 * 1024 * 1024), ec);  
        socket_.set_option(asio::socket_base::receive_buffer_size(8 * 1024 * 1024), ec);  
    }  
  
    void run()  
    {  
        do_read();  
    }  
  
private:  
    tcp::socket socket_;  
    asio::strand<asio::any_io_executor> strand_;  
    beast::flat_buffer buffer_;  
    http::request<http::string_body> req_;  
  
    void do_read()  
    {  
        auto self = shared_from_this();  
        // For single-threaded io_context, strand is unnecessary and hurts performance  
        // Remove strand binding for maximum throughput (only safe if single-threaded)  
        http::async_read(  
            socket_,  
            buffer_,  
            req_,  
            [self](beast::error_code ec, std::size_t) {  
                if (!ec)  
                {  
                    self->handle_request();  
                }  
            });  
    }  
  
    void handle_request()  
    {  
        http::response<http::string_body> res{http::status::ok, req_.version()};  
        res.set(http::field::server, "BeastBench/1.0");  
        res.set(http::field::content_type, "application/octet-stream");  
        res.keep_alive(req_.keep_alive());  
        // Optimized: Use move to avoid copying large bodies  
        res.body() = std::move(req_.body());  
        res.prepare_payload();  
        send_response(std::move(res));  
    }  
  
    void send_response(http::response<http::string_body>&& res)  
    {  
        auto sp = std::make_shared<http::response<http::string_body>>(std::move(res));  
        auto self = shared_from_this();  
  
        // For single-threaded io_context, strand is unnecessary and hurts performance  
        // Remove strand binding for maximum throughput (only safe if single-threaded)  
        http::async_write(  
            socket_,  
            *sp,  
            [sp, self](beast::error_code ec, std::size_t) {  
                if (!ec && sp->keep_alive())  
                {  
                    self->req_ = {};  
                    self->buffer_.consume(self->buffer_.size());  
                    self->do_read();  
                }  
                else if (!ec)  
                {  
                    beast::error_code ec_shutdown;  
                    self->socket_.shutdown(tcp::socket::shutdown_send, ec_shutdown);  
                }  
            });  
    }  
};  
  
// Simplified listener  
class server_listener : public std::enable_shared_from_this<server_listener>  
{  
public:  
    server_listener(asio::any_io_executor exec, tcp::endpoint endpoint)  
        : acceptor_(exec)  
    {  
        beast::error_code ec;  
        acceptor_.open(endpoint.protocol(), ec);  
        acceptor_.set_option(asio::socket_base::reuse_address(true), ec);  
        acceptor_.bind(endpoint, ec);  
        acceptor_.listen(asio::socket_base::max_listen_connections, ec);  
    }  
  
    void run()  
    {  
        if (acceptor_.is_open())  
            do_accept();  
    }  
  
private:  
    tcp::acceptor acceptor_;  
  
    void do_accept()  
    {  
        auto self = shared_from_this();  
        acceptor_.async_accept(  
            asio::make_strand(acceptor_.get_executor()),  
            [self](beast::error_code ec, tcp::socket socket) {  
                if (!ec)  
                {  
                    std::make_shared<server_session>(std::move(socket))->run();  
                }  
                self->do_accept();  
            });  
    }  
};  
  
// Simplified client session  
class client_session : public std::enable_shared_from_this<client_session>  
{  
public:  
    client_session(  
        asio::io_context& ioc,  
        const std::string& host,  
        const std::string& port,  
        const std::string& path,  
        const std::string& body,  
        BenchmarkStats& stats)  
        : resolver_(ioc)  
        , socket_(ioc)  
        , host_(host)  
        , port_(port)  
        , path_(path)  
        , body_(body)  
        , stats_(stats)  
    {  
    }  
  
    void run()  
    {  
        start_time_ = std::chrono::steady_clock::now();  
        resolver_.async_resolve(  
            host_,  
            port_,  
            [self = shared_from_this()](beast::error_code ec, tcp::resolver::results_type results) {  
                if (!ec)  
                {  
                    self->on_resolve(results);  
                }  
                else  
                {  
                    self->stats_.record_failure();  
                }  
            });  
    }  
  
private:  
    tcp::resolver resolver_;  
    tcp::socket socket_;  
    beast::flat_buffer buffer_;  
    http::request<http::string_body> req_;  
    http::response<http::string_body> res_;  
    std::string host_;  
    std::string port_;  
    std::string path_;  
    std::string body_;  
    BenchmarkStats& stats_;  
    std::chrono::steady_clock::time_point start_time_;  
  
    void on_resolve(tcp::resolver::results_type results)  
    {  
        auto self = shared_from_this();  
        asio::async_connect(  
            socket_,  
            results,  
            [self](beast::error_code ec, const tcp::endpoint&) {  
                if (!ec)  
                {  
                    self->on_connect();  
                }  
                else  
                {  
                    self->stats_.record_failure();  
                }  
            });  
    }  
  
    void on_connect()  
    {  
        // Start timing here (after connection is established)  
        // This excludes connection setup time from latency measurement  
        start_time_ = std::chrono::steady_clock::now();  
          
        // Configure socket for maximum performance  
        // These settings are critical for achieving high bandwidth (3GB/s target)  
        beast::error_code ec;  
          
        // Disable Nagle's algorithm for low latency  
        socket_.set_option(tcp::no_delay(true), ec);  
          
        // Use large buffers (8MB) to match epolls_benchmark performance  
        // Small default buffers (64KB-256KB) severely limit bandwidth  
        socket_.set_option(asio::socket_base::send_buffer_size(8 * 1024 * 1024), ec);  
        socket_.set_option(asio::socket_base::receive_buffer_size(8 * 1024 * 1024), ec);  
          
        req_.method(http::verb::put);  
        req_.target(path_);  
        req_.version(11);  
        req_.set(http::field::host, host_);  
        req_.set(http::field::user_agent, "BeastBench/1.0");  
        req_.set(http::field::content_type, "application/octet-stream");  
        req_.set(http::field::connection, "keep-alive");  // Enable keep-alive  
        req_.body() = body_;  
        req_.prepare_payload();  
  
        auto self = shared_from_this();  
        http::async_write(  
            socket_,  
            req_,  
            [self](beast::error_code ec, std::size_t) {  
                if (!ec)  
                {  
                    self->on_write();  
                }  
                else  
                {  
                    self->stats_.record_failure();  
                }  
            });  
    }  
  
    void on_write()  
    {  
        auto self = shared_from_this();  
        http::async_read(  
            socket_,  
            buffer_,  
            res_,  
            [self](beast::error_code ec, std::size_t) {  
                if (!ec)  
                {  
                    self->on_read();  
                }  
                else  
                {  
                    self->stats_.record_failure();  
                }  
            });  
    }  
  
    void on_read()  
    {  
        auto end = std::chrono::steady_clock::now();  
        auto latency = std::chrono::duration<double, std::milli>(end - start_time_).count();  
          
        socket_.shutdown(tcp::socket::shutdown_both);  
          
        if (res_.result() == http::status::ok)  
        {  
            stats_.record_success(latency, body_.size());  
        }  
        else  
        {  
            stats_.record_failure();  
        }  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char* argv[])  
{  
    try  
    {  
        unsigned short port = 8080;  
        int requests = 10000;  
        int concurrency = 50;  
        size_t body_size = 1048576; // 1 MB  
        int server_threads = std::max(1, static_cast<int>(std::thread::hardware_concurrency()));  
  
        // Parse arguments  
        for (int i = 1; i < argc; ++i)  
        {  
            std::string arg = argv[i];  
            if (arg == "--port" && i + 1 < argc)  
            {  
                port = static_cast<unsigned short>(std::stoi(argv[++i]));  
            }  
            else if (arg == "--requests" && i + 1 < argc)  
            {  
                requests = std::stoi(argv[++i]);  
            }  
            else if (arg == "--concurrency" && i + 1 < argc)  
            {  
                concurrency = std::stoi(argv[++i]);  
            }  
            else if (arg == "--size" && i + 1 < argc)  
            {  
                body_size = std::stoull(argv[++i]);  
            }  
            else if (arg == "--threads" && i + 1 < argc)  
            {  
                server_threads = std::stoi(argv[++i]);  
            }  
            else if (arg == "--help" || arg == "-h")  
            {  
                std::cout << "Usage: " << argv[0] << " [OPTIONS]\n"  
                          << "Options:\n"  
                          << "  --port PORT        Server port (default: 8080)\n"  
                          << "  --requests N       Number of requests (default: 10000)\n"  
                          << "  --concurrency N    Concurrent requests (default: 50)\n"  
                          << "  --size N           Request body size in bytes (default: 1048576)\n"  
                          << "  --threads N        Server worker threads (default: CPU count)\n"  
                          << "  --help, -h         Show this help\n";  
                return 0;  
            }  
        }  
  
        std::cout << "Boost.Beast Benchmark - Server + Client\n";  
        std::cout << "========================================\n";  
        std::cout << "Port:          " << port << "\n";  
        std::cout << "Requests:      " << requests << "\n";  
        std::cout << "Concurrency:   " << concurrency << "\n";  
        std::cout << "Body size:     " << body_size << " bytes ("   
                  << (body_size / 1024 / 1024.0) << " MB)\n";  
        std::cout << "Server threads: " << server_threads << "\n\n";  
  
        // Start server in separate thread  
        asio::io_context server_ioc{server_threads};  
        auto address = asio::ip::make_address("0.0.0.0");  
        auto server_lst = std::make_shared<server_listener>(  
            server_ioc.get_executor(),  
            tcp::endpoint{address, port});  
        server_lst->run();  
  
        std::vector<std::thread> server_threads_vec;  
        server_threads_vec.reserve(server_threads - 1);  
        for (auto i = server_threads - 1; i > 0; --i)  
            server_threads_vec.emplace_back([&server_ioc] { server_ioc.run(); });  
  
        // Give server a moment to start  
        std::this_thread::sleep_for(std::chrono::milliseconds(100));  
  
        // Run client benchmark  
        std::string body(body_size, 'X');  
        asio::io_context client_ioc;  
        BenchmarkStats stats;  
        stats.start_time = std::chrono::steady_clock::now();  
  
        // Create and run client sessions  
        // Simple approach: start all requests, let asio handle concurrency  
        std::vector<std::shared_ptr<client_session>> sessions;  
        sessions.reserve(requests);  
  
        for (int i = 0; i < requests; ++i)  
        {  
            auto session = std::make_shared<client_session>(  
                client_ioc, "localhost", std::to_string(port),   
                "/test" + std::to_string(i), body, stats);  
            session->run();  
            sessions.push_back(session);  
        }  
  
        // Run client io_context  
        std::thread client_thread([&client_ioc] { client_ioc.run(); });  
  
        // Wait for all requests to complete  
        // (We'll wait until stats show all requests are done)  
        while (true)  
        {  
            std::this_thread::sleep_for(std::chrono::milliseconds(100));  
            std::lock_guard<std::mutex> lock(stats.mutex);  
            if (stats.success_count + stats.failure_count >= requests)  
                break;  
        }  
  
        // Stop and wait  
        client_ioc.stop();  
        client_thread.join();  
        stats.end_time = std::chrono::steady_clock::now();  
  
        // Stop server  
        server_ioc.stop();  
        for (auto& t : server_threads_vec)  
            t.join();  
  
        stats.print_results();  
    }  
    catch (const std::exception& e)  
    {  
        std::cerr << "Error: " << e.what() << "\n";  
        return 1;  
    }  
  
    return 0;  
}  
  
  
  
```  
could you please shed some light?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2026-01-03 04:24:22 UTC  
> Url: https://github.com/boostorg/beast/issues/3064#issuecomment-3706684191  

What platform?

---

## Comment 2

> Username: gaowayne  
> Created at: 2026-01-03 10:38:10 UTC  
> Url: https://github.com/boostorg/beast/issues/3064#issuecomment-3706963616  

> What platform?  
  
I am using AMD latest gen5 platform that can saturate 128x gen5 NVMe SSD each one is 10GB/s BW.  
 ```  
root@salab-smcamd02:~# lscpu  
Architecture:                x86_64  
  CPU op-mode(s):            32-bit, 64-bit  
  Address sizes:             52 bits physical, 57 bits virtual  
  Byte Order:                Little Endian  
CPU(s):                      128  
  On-line CPU(s) list:       0-127  
Vendor ID:                   AuthenticAMD  
  BIOS Vendor ID:            Advanced Micro Devices, Inc.  
  Model name:                AMD EPYC 9554P 64-Core Processor  
    BIOS Model name:         AMD EPYC 9554P 64-Core Processor                Unknown CPU @ 3.1GHz  
    BIOS CPU family:         107  
    CPU family:              25  
    Model:                   17  
    Thread(s) per core:      2  
    Core(s) per socket:      64  
    Socket(s):               1  
    Stepping:                1  
    Frequency boost:         enabled  
    CPU(s) scaling MHz:      50%  
    CPU max MHz:             3100.0000  
    CPU min MHz:             1500.0000  
    BogoMIPS:                6190.57  
    Flags:                   fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext fxsr_opt pdpe1gb rdtscp lm constant_tsc rep_good amd_lbr_v2 nopl xtopology nonstop_tsc cpuid extd_apicid aperfmp  
                             erf rapl pni pclmulqdq monitor ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx f16c rdrand lahf_lm cmp_legacy svm extapic cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw ibs skinit wdt tce topoext perfctr_co  
                             re perfctr_nb bpext perfctr_llc mwaitx cpb cat_l3 cdp_l3 hw_pstate ssbd mba perfmon_v2 ibrs ibpb stibp ibrs_enhanced vmmcall fsgsbase bmi1 avx2 smep bmi2 erms invpcid cqm rdt_a avx512f avx512dq rdseed adx smap avx512ifma clflushopt  
                              clwb avx512cd sha_ni avx512bw avx512vl xsaveopt xsavec xgetbv1 xsaves cqm_llc cqm_occup_llc cqm_mbm_total cqm_mbm_local user_shstk avx512_bf16 clzero irperf xsaveerptr rdpru wbnoinvd amd_ppin cppc arat npt lbrv svm_lock nrip_save  
                             tsc_scale vmcb_clean flushbyasid decodeassists pausefilter pfthreshold avic v_vmsave_vmload vgif x2avic v_spec_ctrl vnmi avx512vbmi umip pku ospke avx512_vbmi2 gfni vaes vpclmulqdq avx512_vnni avx512_bitalg avx512_vpopcntdq la57 rd  
                             pid overflow_recov succor smca fsrm flush_l1d debug_swap  
Virtualization features:  
  Virtualization:            AMD-V  
Caches (sum of all):  
  L1d:                       2 MiB (64 instances)  
  L1i:                       2 MiB (64 instances)  
  L2:                        64 MiB (64 instances)  
  L3:                        256 MiB (8 instances)  
NUMA:  
  NUMA node(s):              1  
  NUMA node0 CPU(s):         0-127  
Vulnerabilities:  
  Gather data sampling:      Not affected  
  Ghostwrite:                Not affected  
  Indirect target selection: Not affected  
  Itlb multihit:             Not affected  
  L1tf:                      Not affected  
  Mds:                       Not affected  
  Meltdown:                  Not affected  
  Mmio stale data:           Not affected  
  Reg file data sampling:    Not affected  
  Retbleed:                  Not affected  
  Spec rstack overflow:      Mitigation; Safe RET  
  Spec store bypass:         Mitigation; Speculative Store Bypass disabled via prctl  
  Spectre v1:                Mitigation; usercopy/swapgs barriers and __user pointer sanitization  
  Spectre v2:                Mitigation; Enhanced / Automatic IBRS; IBPB conditional; STIBP always-on; PBRSB-eIBRS Not affected; BHI Not affected  
  Srbds:                     Not affected  
  Tsx async abort:           Not affected  
```  
```  
root@salab-smcamd02:~# uname -r  
6.15.0-061500-generic  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2026-01-03 15:19:12 UTC  
> Url: https://github.com/boostorg/beast/issues/3064#issuecomment-3707126804  

Yeah but what operating system? Windows? Linux?

---

## Comment 4

> Username: ashtum  
> Created at: 2026-01-04 10:13:53 UTC  
> Updated at: 2026-01-04 10:32:03 UTC  
> Url: https://github.com/boostorg/beast/issues/3064#issuecomment-3707940076  

Your benchmark has several issues that distort the results:  
  
- Startup overhead is included in the request–response timing.  
- Only a single client thread is used, while the server runs with multiple threads.  
- The request body is unnecessarily copied.  
- The same data is transferred twice (sent in the request and echoed in the response), but it is counted only once in the measurement.  
- You’re adding threads to a task that is primarily limited by I/O and memory bandwidth.  
- Because launching many concurrent connections with large payloads leads to frequent CPU cache misses, it’s not fair to compare this scenario to a single TCP loop writing just one buffer.  
  
With a few small adjustments, the benchmark reaches 820.89 MB/s on my machine (Intel i7-13700). Since the response payload is not included in the measurement, this effectively corresponds to about 1.6 GB/s of total throughput:  
https://gist.github.com/ashtum/7e590993e72f0595e185f6698ca36858  
  
```  
Boost.Beast Benchmark - Server + Client  
========================================  
Port:          9080  
Requests:      1000  
Body size:     1048576 bytes (1 MB)  
Server threads: 8  
  
  
======================================================================  
Boost.Beast Benchmark Results  
======================================================================  
Total requests:  1000  
  Success:      1000  
  Failed:       0  
Duration:       1.28 seconds  
Throughput:     6567.13 Mbps (820.89 MB/s)  
Operations/sec: 782.86  
  
Latency (milliseconds):  
  Min:          44.76  
  Max:          1364.03  
  Average:      1093.69  
  Median (p50): 1295.52  
  p95:          1362.21  
  p99:          1363.75  
======================================================================  
```  
  
I’d suggest starting with a simple single-threaded server and using ab or similar client-side tools to minimize tweaking, while you get more familiar with the problem.  
  
Measuring raw throughput on an HTTP server often yields little insight, since I/O and memory bandwidth usually become the limiting factors, making most libraries perform similarly. A more meaningful metric for comparing server performance is **requests per second**.  
  
Related performance topics:  
https://github.com/boostorg/beast/issues/2947  
https://github.com/boostorg/beast/issues/3051  
https://github.com/boostorg/beast/issues/2831

---

## Comment 5

> Username: gaowayne  
> Created at: 2026-01-04 12:47:00 UTC  
> Url: https://github.com/boostorg/beast/issues/3064#issuecomment-3708057768  

> Yeah but what operating system? Windows? Linux?  
  
Linux ubuntu  
```  
root@salab-smcamd02:~# cat /etc/os-release  
PRETTY_NAME="Ubuntu 24.04.1 LTS"  
NAME="Ubuntu"  
VERSION_ID="24.04"  
VERSION="24.04.1 LTS (Noble Numbat)"  
VERSION_CODENAME=noble  
ID=ubuntu  
ID_LIKE=debian  
HOME_URL="https://www.ubuntu.com/"  
SUPPORT_URL="https://help.ubuntu.com/"  
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"  
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"  
UBUNTU_CODENAME=noble  
LOGO=ubuntu-logo  
root@salab-smcamd02:~#  
```

---

## Comment 6

> Username: gaowayne  
> Created at: 2026-01-04 12:55:41 UTC  
> Url: https://github.com/boostorg/beast/issues/3064#issuecomment-3708063753  

> Your benchmark has several issues that distort the results:  
>   
> * Startup overhead is included in the request–response timing.  
> * Only a single client thread is used, while the server runs with multiple threads.  
> * The request body is unnecessarily copied.  
> * The same data is transferred twice (sent in the request and echoed in the response), but it is counted only once in the measurement.  
> * You’re adding threads to a task that is primarily limited by I/O and memory bandwidth.  
> * Because launching many concurrent connections with large payloads leads to frequent CPU cache misses, it’s not fair to compare this scenario to a single TCP loop writing just one buffer.  
>   
> With a few small adjustments, the benchmark reaches 820.89 MB/s on my machine (Intel i7-13700). Since the response payload is not included in the measurement, this effectively corresponds to about 1.6 GB/s of total throughput: https://gist.github.com/ashtum/7e590993e72f0595e185f6698ca36858  
>   
> ```  
> Boost.Beast Benchmark - Server + Client  
> ========================================  
> Port:          9080  
> Requests:      1000  
> Body size:     1048576 bytes (1 MB)  
> Server threads: 8  
>   
>   
> ======================================================================  
> Boost.Beast Benchmark Results  
> ======================================================================  
> Total requests:  1000  
>   Success:      1000  
>   Failed:       0  
> Duration:       1.28 seconds  
> Throughput:     6567.13 Mbps (820.89 MB/s)  
> Operations/sec: 782.86  
>   
> Latency (milliseconds):  
>   Min:          44.76  
>   Max:          1364.03  
>   Average:      1093.69  
>   Median (p50): 1295.52  
>   p95:          1362.21  
>   p99:          1363.75  
> ======================================================================  
> ```  
>   
> I’d suggest starting with a simple single-threaded server and using ab or similar client-side tools to minimize tweaking, while you get more familiar with the problem.  
>   
> Measuring raw throughput on an HTTP server often yields little insight, since I/O and memory bandwidth usually become the limiting factors, making most libraries perform similarly. A more meaningful metric for comparing server performance is **requests per second**.  
>   
> Related performance topics: [#2947](https://github.com/boostorg/beast/issues/2947) [#3051](https://github.com/boostorg/beast/issues/3051) [#2831](https://github.com/boostorg/beast/issues/2831)  
  
great suggestion. I will follow your suggestion with this. :), I may not get back this very soon. could you please keep this open for monitoring.
