# #209 - There is a bug in boost/interprocess/detail/os_thread_function.hpp [Open]

> Username: xuyiwen245  
> Created at: 2024-01-22 08:29:01 UTC  
> Updated at: 2024-01-22 08:29:01 UTC  
> Url: https://github.com/boostorg/interprocess/issues/209  

Hello, Mr. Author. On the Linux platform, my software keeps creating and destroying processes. Sometimes, when we create a process again, the program gets stuck in the while loop of   function( static void initialize_singleton_logic  function in )  boost/interprocess/detail/intermodule_singleton_common.hpp  
            while(1){  
               previous_module_singleton_initialized = atomic_read32(&this_module_singleton_initialized);  
               if(previous_module_singleton_initialized >= Initialized){  
                  //Already initialized, or exception thrown by initializer thread  
                  break;  
               }  
               else if(previous_module_singleton_initialized == Initializing){  
                  swait.yield();  
               }  
               else{  
                  //This can't be happening!  
                  BOOST_ASSERT(0);  
               }  
After investigation, I found that it was because latest the process ID  was the same as the ID of a historical process (which had already exited).   
The shared memory name  include process id , process creation time  and a base name as below.  
inline void get_map_name(std::string &map_name) //intermodule_singleton_common.hpp  
{  
   get_pid_creation_time_str(map_name);  
   map_name.insert(0, get_map_base_name());  
}  
  
inline void get_pid_creation_time_str(std::string &s)  
{  
   std::stringstream stream;  
   stream << get_current_process_id() << '_';  
   stream.precision(6);  
   stream << std::fixed << get_current_process_creation_time();  
   s = stream.str();  
}  
  
inline long double get_current_process_creation_time()  //os_thread_functions.hpp  
{ return 0.0L; }  
  
But on linux OS, get_current_process_creation_time alway return a fixed 0.0. So once the id of new process is same as the history process , The new process gets stuck.
