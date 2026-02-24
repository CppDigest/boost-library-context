# #225 - "When using boost::interprocess to manage a shared memory map, I encounter errors. Where might the problem be? [Closed]

> Username: iamysx  
> Created at: 2024-07-29 09:29:53 UTC  
> Updated at: 2024-09-26 21:05:37 UTC  
> Closed at: 2024-09-26 21:05:36 UTC  
> Url: https://github.com/boostorg/interprocess/issues/225  

#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/interprocess/containers/map.hpp>  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/sync/interprocess_mutex.hpp>  
#include <boost/interprocess/sync/scoped_lock.hpp>  
#include <functional>  
#include <utility>  
#include <iostream>  
#include <sys/wait.h>  
#include <unistd.h>  
  
typedef int KeyType;  
typedef float MappedType;  
typedef std::pair<const int, float> ValueType;  
using namespace boost::interprocess;  
  
const char *shared_memory_name = "MySharedMemory";  
  
// Remove shared memory on construction and destruction  
struct shm_remove {  
    shm_remove() { shared_memory_object::remove(shared_memory_name); }  
    ~shm_remove() { shared_memory_object::remove(shared_memory_name); }  
} remover;  
  
struct SharedData {  
    interprocess_mutex mutex;  
    typedef allocator<ValueType, managed_shared_memory::segment_manager> ShmemAllocator;  
    typedef map<KeyType, MappedType, std::less<KeyType>, ShmemAllocator> MyMap;  
    ShmemAllocator alloc_inst;  
    MyMap mymap;  
  
    SharedData(managed_shared_memory::segment_manager *segment_manager)  
        : alloc_inst(segment_manager), mymap(std::less<KeyType>(), alloc_inst) {}  
};  
  
void child_process(int id) {  
    try {  
        // Open the shared memory  
        managed_shared_memory segment(open_only, shared_memory_name);  
        SharedData *data = segment.find<SharedData>("MySharedData").first;  
  
        if (data) {  
            // Lock the mutex  
            scoped_lock<interprocess_mutex> lock(data->mutex);  
  
            // Insert data specific to this child process  
            data->mymap.insert(ValueType(id, static_cast<float>(id)));  
  
            // Print data inserted by this child process  
            std::cout << "Child " << id << " inserted value " << id << std::endl;  
        }  
    } catch (const std::exception &e) {  
        std::cerr << "Child process exception: " << e.what() << std::endl;  
    }  
}  
  
int main() {  
    const int num_children = 1;  
  
    // Create shared memory  
    managed_shared_memory segment(create_only, shared_memory_name, 65536);  
  
    // Initialize the shared data structure with map and mutex  
    SharedData *data = segment.construct<SharedData>("MySharedData")(segment.get_segment_manager());  
  
    // Insert initial data  
    {  
        scoped_lock<interprocess_mutex> lock(data->mutex);  
        data->mymap.insert(ValueType(0, 0.0f));  
    }  
  
    // Fork child processes  
    for (int i = 1; i <= num_children; ++i) {  
        pid_t pid = fork();  
        if (pid == 0) {  
            child_process(i);  
            return 0;  
        }  
    }  
  
    // Wait for all children to finish  
    for (int i = 1; i <= num_children; ++i) {  
        wait(NULL);  
    }  
  
    // Print data from parent process  
    {  
        scoped_lock<interprocess_mutex> lock(data->mutex);  
        std::cout << "Parent process reading map values:" << std::endl;  
        for (const auto &item : data->mymap) {  
            std::cout << "Key: " << item.first << ", Value: " << item.second << std::endl;  
        }  
    }  
  
    // Destroy the shared data structure (only parent should do this)  
    segment.destroy<SharedData>("MySharedData");  
  
    std::cout << "done" << std::endl;  
    return 0;  
}  
  
**result:**  
Child 1 inserted value 1  
Child 2 inserted value 2  
Child process exception: No such file or directory  
Parent process reading map values:  
Key: 0, Value: 0  
Key: 1, Value: 1  
Key: 2, Value: 2  
done

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-08-05 21:24:43 UTC  
> Url: https://github.com/boostorg/interprocess/issues/225#issuecomment-2269945026  

Without a fully compilable example and the details of your compilation platform it's difficult to know which is the problem.  
  
But I suspect that as soon as one child process exits, the destructor for "remover" runs (since it shares code with the parent process the destructor of "remover" deletes the shared memory, so later running children can't open it.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-09-26 21:05:36 UTC  
> Url: https://github.com/boostorg/interprocess/issues/225#issuecomment-2377936631  

Closing the issue as it looks like programming error.
