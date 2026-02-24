# #144 - Crash in ipcdetail::get_bootstamp when multi thread racing and winapi::get_last_bootup_time failed on Windows [Open]

> Username: lwttai  
> Created at: 2021-05-21 08:50:40 UTC  
> Updated at: 2021-05-21 08:50:40 UTC  
> Url: https://github.com/boostorg/interprocess/issues/144  

Crash in bellow function where windows_intermodule_singleton<windows_bootstamp>::get() return null which cause invalid access.  
  
`  
// boost\interprocess\detail\shared_dir_helpers.hpp: 54  
inline void get_bootstamp(std::string &s, bool add = false) {  
         const windows_bootstamp &bootstamp = windows_intermodule_singleton<windows_bootstamp>::get();  
         if(add){  
            s += bootstamp.stamp;  
         }  
         else{  
            s = bootstamp.stamp;  
         }  
}  
`  
  
  
Debug code example:  
`  
std::atomic_bool g_start = false;  
  
int main() {  
  
	auto thread_func = []() {  
		while (true) {  
			if (g_start) {  
				boost::interprocess::named_mutex::remove("aaa");  
			}  
		}  
	};  
  
	std::thread t1(thread_func);  
	std::thread t2(thread_func);  
	std::this_thread::sleep_for(std::chrono::seconds(1));  
	g_start = true;  
	t1.join();  
}  
`  
When two thread enter intermodule_singleton_common::initialize_singleton_logic(boost\interprocess\detail\intermodule_singleton_common.hpp: 98)，one is waiting in `if(previous_module_singleton_initialized == Initializing)` while another is try to initialize the instance in `if(previous_module_singleton_initialized == Uninitialized)`. If exception throw in the initialize function, the initial thread will set the initialized state to broken which cause the waiting thread stop waiting and return a nullptr without any initialized failed checking or throw any exception.  
  
As the example, windows_bootstamp's construction will throw exception when winapi::get_last_bootup_time return false witch will happen when some user environment can't find System Event Log 6005(event log started) or any other errors.
