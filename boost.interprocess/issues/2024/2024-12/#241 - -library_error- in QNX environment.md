# #241 - "library_error" in QNX environment [Open]

> Username: xyy-jia  
> Created at: 2024-12-02 01:51:02 UTC  
> Updated at: 2026-01-07 08:42:57 UTC  
> Url: https://github.com/boostorg/interprocess/issues/241  

`#include <iostream>  
#include <boost/interprocess/ipc/message_queue.hpp>  
#include <boost/interprocess/permissions.hpp>  
  
  
  
int main(){  
  
    boost::interprocess::permissions permissions;  
    printf("***************************** 1\n");  
  
  
    int status = permissions.get_permissions();  
    printf("***************************** 2 [0x%x]\n",status);  
  
  
  
    // permissions.set_permissions(status);   
    // permissions.set_default();  
    permissions.set_unrestricted();  
    printf("***************************** 3.3\n");  
  
  
  
    boost::interprocess::message_queue mq(boost::interprocess::open_or_create,"my_queue2",100,200);  
    // mq.close();  
    printf("***************************** 4\n");  
  
  
  
    mq.remove("my_queue");  
    printf("***************************** 5\n");  
  
    return 0;  
}`  
  
Always report an error in the QNX environment:  
erminate called after throwing an instance of 'boost::interprocess::interprocess_exception'  
what(): boost::interprocess_exception::library_error  
  
https://github.com/cppalliance/boost-gecko/issues/150#issuecomment-2507587645

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-07 08:42:57 UTC  
> Url: https://github.com/boostorg/interprocess/issues/241#issuecomment-3717854156  

Reviewing old bugs... I don't have access to a Qnx machine, was the problem fixed or patched so that we can incorporate the fix to the library. Thanks!
