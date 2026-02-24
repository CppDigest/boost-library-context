# #71 - boost interprocess rbtree_best_fit crash [Closed]

> Username: 363568233  
> Created at: 2018-12-03 02:49:37 UTC  
> Updated at: 2021-05-10 13:15:02 UTC  
> Closed at: 2021-05-10 13:15:02 UTC  
> Url: https://github.com/boostorg/interprocess/issues/71  

boost_1_65_1  boost::unordered_map in shared_memory ， insert、delete、find  at random 0~10milliion times  in 3 threads, but it crash in rbtree_best_fit,   I used windows mutex to sync

---

## Comment 1

> Username: 363568233  
> Created at: 2018-12-03 06:50:52 UTC  
> Url: https://github.com/boostorg/interprocess/issues/71#issuecomment-443606600  

bstree_algorithms.hpp  
 //Now adjust adjacent nodes for newly inserted node  
      if((temp = NodeTraits::get_left(new_node))){  
         NodeTraits::set_parent(temp, new_node);  
      }

---

## Comment 2

> Username: 363568233  
> Created at: 2018-12-05 09:08:20 UTC  
> Url: https://github.com/boostorg/interprocess/issues/71#issuecomment-444411921  

and boost interprocess rbtree_best_fit inside mutex is deadlock when process crash in it   
Can I  use my interprocess_mutex instead of the rbtree_best_fit's default mutex?

---

## Comment 3

> Username: 363568233  
> Created at: 2018-12-11 06:17:06 UTC  
> Url: https://github.com/boostorg/interprocess/issues/71#issuecomment-446085612  

Now, I'm reading and writing the memory mapped file, offset boost::interprocess::ipcdetail::shmem_open_or_create<boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family> >::type::ManagedOpenOrCreateUserOffset bytes(8 bytes on Windows)  
the ninth byte is the mutex state(locked /unlocked), I change it to 0 can unlock it. Is it will cause any other problems?

---

## Comment 4

> Username: 363568233  
> Created at: 2018-12-13 07:46:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/71#issuecomment-446872778  

![image](https://user-images.githubusercontent.com/6778018/49923358-33d3f300-feee-11e8-9622-f4eed766b18f.png)

---

## Comment 5

> Username: 363568233  
> Created at: 2018-12-14 01:35:04 UTC  
> Updated at: 2018-12-14 08:52:46 UTC  
> Url: https://github.com/boostorg/interprocess/issues/71#issuecomment-447182633  

It seems to crash when the mapped file size more than 4G??   The 3G size running well one night..

---

## Comment 6

> Username: 363568233  
> Created at: 2018-12-14 08:54:58 UTC  
> Url: https://github.com/boostorg/interprocess/issues/71#issuecomment-447258864  

When I insert 4G data, and clear it, the process crashed in clear function? Is there a limit with rbtree_best_fit ? @igaztanaga

---

## Comment 7

> Username: igaztanaga  
> Created at: 2021-05-10 13:15:02 UTC  
> Url: https://github.com/boostorg/interprocess/issues/71#issuecomment-836685489  

Closing old bugs with no activity and no enough information to reproduce the issue. Please reopen it with additional information (a test case would be ideal).
