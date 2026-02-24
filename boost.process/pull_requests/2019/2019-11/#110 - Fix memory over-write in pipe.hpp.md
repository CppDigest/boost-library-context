# #110 Fix memory over-write in pipe.hpp [Merged]

> Username: sorayuki  
> Created at: 2019-11-25 07:38:42 UTC  
> Updated at: 2020-01-26 08:17:28 UTC  
> Merged at: 2020-01-26 08:17:28 UTC  
> Closed at: 2020-01-26 08:17:28 UTC  
> Url: https://github.com/boostorg/process/pull/110  

The child process may exit at any time.  
When the child process is existed, writing to pipe may failed.  
In this case pptr is still pointing to the same address as epptr, which should not be written.  
It causes the heap corrupted.  
  
# How it happens  
  
add these code before include boost headers:  
```  
	std::mutex m;  
	std::set<intptr_t> mems;  
  
	void* operator new(std::size_t n)  
	{  
		if (n == 1024)  
		{  
			auto p = VirtualAlloc(nullptr, 8192, MEM_RESERVE, PAGE_READWRITE);  
			auto r = VirtualAlloc(p, 4096, MEM_COMMIT, PAGE_READWRITE);  
			auto x = (void*)((char*)p + 1024 * 3);  
  
			std::stringstream ss;  
			ss << "1024 byte from " << x << std::endl;  
			OutputDebugStringA(ss.str().c_str());  
  
			std::unique_lock<std::mutex> lg(m);  
			mems.insert((intptr_t)x);  
			return x;  
		}  
		else  
		{  
			return malloc(n);  
		}  
	}  
  
	void operator delete(void* p)  
	{  
		std::unique_lock<std::mutex> lg(m);  
		auto x = (intptr_t)p;  
		auto it = mems.find(x);  
		if (it != mems.end())  
		{  
			VirtualFree((void*)(x - 1024 * 3), 8192, MEM_RELEASE);  
		}  
		else  
		{  
			free(p);  
		}  
	}  
```  
  
use ffmpeg to accept a RTMP client,  
launch `MPV` player as child process to play the stream  
```  
    AVIOContext* ctx = nullptr;  
    AVDictionary* opt = nullptr;  
  
    auto ret = [&](bool val = false) {  
        if (opt)  
            av_dict_free(&opt);  
        if (ctx)  
            avio_closep(&ctx);  
        return val;  
    };  
  
    av_dict_set_int(&opt, "listen", 1, 0);  
    if (avio_open2(&ctx, "rtmp://0.0.0.0:1935/test", AVIO_FLAG_READ, nullptr, &opt) < 0) {  
        return ret(false);  
    }  
  
    try {  
        bp::opstream mpv_in;  
        mpv_in.setf(std::ios::binary);  
        bp::child proc("mpv -", bp::std_in < mpv_in);  
  
        std::vector<uint8_t> buf(2000);  
  
        auto previousUpdateTime = steady_clock::now();  
        int bytesCount = 0;  
  
        for(;;) {  
            int readsize = avio_read_partial(ctx, buf.data(), buf.size());  
            if (readsize <= 0)  
            {  
                mpv_in.pipe().close();  
                break;  
            }  
              
            if (!proc.running())  
                break;  
  
            mpv_in.write((const char*)buf.data(), readsize);  
        }  
        proc.join();  
  
        return ret(true);  
    }  
    catch(const std::exception& e)  
    {  
        return ret(false);  
    }  
```  
  
during playing, close the mpv player.  
If it didn't crash (breaks in `*this->pptr() = ch;`), re-run it.  
It's not hard to replay the issue in my computer.

---
