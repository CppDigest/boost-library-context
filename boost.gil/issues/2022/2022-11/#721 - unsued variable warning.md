# #721 - unsued variable warning [Closed]

> Username: dia2e2  
> Created at: 2022-11-14 19:32:25 UTC  
> Updated at: 2022-11-14 22:30:32 UTC  
> Closed at: 2022-11-14 22:30:32 UTC  
> Url: https://github.com/boostorg/gil/issues/721  

Anyone knows why I keep getting this error.  
  
```  
make -C /lib/modules/5.15.0-52-generic/build M=/home/alex/findtask modules  
make[1]: Entering directory '/usr/src/linux-headers-5.15.0-52-generic'  
warning: the compiler differs from the one used to build the kernel  
  The kernel was built by: gcc (Ubuntu 11.2.0-19ubuntu1) 11.2.0  
  You are using:           gcc (Ubuntu 11.3.0-1ubuntu1~22.04) 11.3.0  
  CC [M]  /home/alex/findtask/findTask2.o  
/home/alex/findtask/findTask2.c: In function ‘mytimer_callback’:  
/home/alex/findtask/findTask2.c:20:21: warning: unused variable ‘task’ [-Wunused-variable]  
   20 | struct task_struct *task;  
      |                     ^~~~  
In file included from ./include/linux/kernel.h:19,  
                 from ./include/linux/list.h:9,  
                 from ./include/linux/module.h:12,  
                 from /home/alex/findtask/findTask2.c:1:  
/home/alex/findtask/findTask2.c: In function ‘findTask2_init’:  
/home/alex/findtask/findTask2.c:27:58: error: expected expression before ‘->’ token  
   27 | printk(KERN_INFO "Found process %s with pid %d\n", name, ->pid);  
      |                                                          ^~  
./include/linux/printk.h:422:33: note: in definition of macro ‘printk_index_wrap’  
  422 |                 _p_func(_fmt, ##__VA_ARGS__);                           \  
      |                                 ^~~~~~~~~~~  
/home/alex/findtask/findTask2.c:27:1: note: in expansion of macro ‘printk’  
   27 | printk(KERN_INFO "Found process %s with pid %d\n", name, ->pid);  
      | ^~~~~~  
make[2]: *** [scripts/Makefile.build:297: /home/alex/findtask/findTask2.o] Error 1  
make[1]: *** [Makefile:1900: /home/alex/findtask] Error 2  
make[1]: Leaving directory '/usr/src/linux-headers-5.15.0-52-generic'  
make: *** [Makefile:4: all] Error 2  
```
