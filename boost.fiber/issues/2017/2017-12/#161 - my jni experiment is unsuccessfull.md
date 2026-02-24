# #161 - my jni experiment is unsuccessfull [Closed]

> Username: publicocean0  
> Created at: 2017-12-24 02:57:06 UTC  
> Updated at: 2017-12-24 14:43:39 UTC  
> Closed at: 2017-12-24 12:22:33 UTC  
> Url: https://github.com/boostorg/fiber/issues/161  

Hi i developped a mini project in java with JNI for fiber porting.   
  
I i execute a fiber executing code in c++ world all is ok but if i execute a function containing also java code   
it gives  
  
```  
 1---  
Exception in thread "main" java.lang.StackOverflowError  
 31---  
	at ext.concurrent.fiber.NativeFiber.run(Native Method)  
 32---  
	at ext.concurrent.fiber.NativeFiber.start(NativeFiber.java:82)  
 3---xx  
	at ext.concurrent.Test.main(Test.java:16)  
  
```  
Probably when the thread context is restored it copy the stack of VM  in wrong way  
This means JNI is no more working inside fibers . Java world is broken . I m very sad for this info :(  
  
Maybe just the opposite versus could work calling c++ from a java fiber, but there is another problem ... about locks in c++. I might implement a jni locks library to use in c++. Pratically instead to port c++ to java i might port java to c++ for working java fiber. This is my ipothesis working.    
  
```  
inline void execute(JNIEnv * env,jobject runnable,jmethodID mid){  
 cout <<  " 31---"  << endl;  
 env->CallVoidMethod(runnable, mid);  
  cout <<  " 32---"  << endl;  
}  
JNIEXPORT void JNICALL Java_ext_concurrent_fiber_NativeFiber_run  
  (JNIEnv * env, jclass clazz, jobject runnable){  
cout <<  " 1---"  << endl;  
   jclass cls = env->GetObjectClass( runnable);  
  // cout <<  " 2---"  << endl;  
   jmethodID mid = env->GetMethodID( cls, "run", "()V");  
//cout <<  " 3---"  << endl;  
  
  
  boost::fibers::fiber fiber(execute,env,runnable,mid);  
  fiber.join();  
  cout <<  " 3---xx"  << endl;  
  
  }  
  
  
```  
  
```  
    public static void main(String[] args) throws InterruptedException {  
  
        NativeFiber fiber=new NativeFiber(new Runnable() {  
            @Override  
            public void run() {  
  
  
                System.out.println("hello");  
            }  
        });  
        fiber.start();  
        fiber.join();  
  
    }  
}  
```  
I dont know how to retrieve a previous fiber ,anyway here for test  i put fiber.join inside the same jni_run  
  
  
if i remove fiber.join() and i add fiber.detach():  
```  
#  
# A fatal error has been detected by the Java Runtime Environment:  
#  
#  SIGSEGV (0xb) at pc=0x00007f5a37017f1f, pid=16559, tid=0x00007f5a38522700  
#  
# JRE version: Java(TM) SE Runtime Environment (8.0_131-b11) (build 1.8.0_131-b11)  
# Java VM: Java HotSpot(TM) 64-Bit Server VM (25.131-b11 mixed mode linux-amd64 compressed oops)  
# Problematic frame:  
# V  [libjvm.so+0x6d7f1f]  jni_CallVoidMethodV+0x3f  
#  
# Failed to write core dump. Core dumps have been disabled. To enable core dumping, try "ulimit -c unlimited" before starting Java again  
#  
# An error report file with more information is saved as:  
# /data/git/concurrent/hs_err_pid16559.log  
#  
# If you would like to submit a bug report, please visit:  
#   http://bugreport.java.com/bugreport/crash.jsp  
```

---

## Comment 1

> Username: publicocean0  
> Created at: 2017-12-24 09:40:37 UTC  
> Updated at: 2017-12-24 13:25:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/161#issuecomment-353775136  

anyway this is the proof that all the languages embedded a custom fibers/cotoutines are following a wrong way, the right way is to implement a good library with fibers at OS kernel level.

---

## Comment 2

> Username: olk  
> Created at: 2017-12-24 12:21:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/161#issuecomment-353781329  

Stackoverflow means that the Stack size is too small. Try thecrate the  
fiber with a stack size of 1MB.  
  
Am 24.12.2017 03:57 schrieb "publicocean0" <notifications@github.com>:  
  
> Hi i developped a mini project in java with JNI for fiber porting.  
>  
> I i execute a fiber executing code in c++ world all is ok but if i execute  
> a function containing also java code  
> it gives  
>  
> Exception in thread "main" java.lang.StackOverflowError  
>  2---  
> 	at ext.concurrent.fiber.NativeFiber.run(Native Method)  
>  3---  
> 	at ext.concurrent.fiber.NativeFiber.start(NativeFiber.java:82)  
> hello  
> 	at ext.concurrent.Test.main(Test.java:16)  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/fiber/issues/161>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AA30QMs18vz4h7jYcfb1UZtXOlpyMTT7ks5tDb2EgaJpZM4RL0XI>  
> .  
>

---

## Comment 3

> Username: olk  
> Created at: 2017-12-24 12:21:46 UTC  
> Url: https://github.com/boostorg/fiber/issues/161#issuecomment-353781342  

No  
  
Am 24.12.2017 10:40 schrieb "publicocean0" <notifications@github.com>:  
  
> anyway this is the proof that all the languages embedded a custom  
> fibers/cotoutines are following a wrong way, the right way is to implement  
> a good library at OS kernel level.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/fiber/issues/161#issuecomment-353775136>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AA30QJV7EbtP-Exh5oq7VAZSna9TjFRoks5tDhwVgaJpZM4RL0XI>  
> .  
>

---

## Comment 4

> Username: publicocean0  
> Created at: 2017-12-24 13:22:47 UTC  
> Updated at: 2017-12-24 13:22:56 UTC  
> Url: https://github.com/boostorg/fiber/issues/161#issuecomment-353784048  

struct rlimit rl;  
const rlim_t kStackSize = 16 * 1024 * 1024;  
getrlimit(RLIMIT_STACK, &rl);  
rl.rlim_cur = kStackSize;  
rl.rlim_max=kStackSize;  
 setrlimit(RLIMIT_STACK, &rl);  
 boost::fibers::fiber(execute,env,runnable,mid ).detach();  
  
same error

---

## Comment 5

> Username: olk  
> Created at: 2017-12-24 13:25:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/161#issuecomment-353784171  

No, you have to specify the stack size at fiber's ctor ... see boost.fiber  
docu/examples  
  
Am 24.12.2017 14:22 schrieb "publicocean0" <notifications@github.com>:  
  
> struct rlimit rl;  
> const rlim_t kStackSize = 16 * 1024 * 1024;  
> getrlimit(RLIMIT_STACK, &rl);  
> rl.rlim_cur = kStackSize;  
> rl.rlim_max=kStackSize;  
> setrlimit(RLIMIT_STACK, &rl);  
> boost::fibers::fiber(execute,env,runnable,mid ).detach();  
>  
> there error  
>  
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/fiber/issues/161#issuecomment-353784048>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AA30QGzU3dcGkHGBE58Yye4ya9ATCgafks5tDlAngaJpZM4RL0XI>  
> .  
>

---

## Comment 6

> Username: publicocean0  
> Created at: 2017-12-24 13:43:23 UTC  
> Updated at: 2017-12-24 14:43:39 UTC  
> Url: https://github.com/boostorg/fiber/issues/161#issuecomment-353784998  

boost::fibers::fiber(std::allocator_arg,  
                                              				boost::fibers::fixedsize_stack(  
                                              					1024*1024),std::bind(&JNIEnv::CallVoidMethod,env,runnable,mid) ).detach();  
  
or  boost::fibers::fiber(std::allocator_arg,  
                                              				boost::fibers::fixedsize_stack(  
                                              					16*1024*1024),execute,env,runnable,mid ).detach();  
  
same error(also encreasing size until to 16mb)  
 I m thinking to change strategy.... instead to use a java callback i use a bytearray(result) or a serialized data that it is passed to java when fiber is completed. This attempt works.  I think if i use a thread pool equal to processors (one foreach processor) i can reach the maximun  power .It is not the best solution but propably the unique working in the current JVM
