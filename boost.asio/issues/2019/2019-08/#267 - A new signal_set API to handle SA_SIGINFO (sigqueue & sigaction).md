# #267 - A new signal_set API to handle SA_SIGINFO (sigqueue & sigaction) [Closed]

> Username: skannan89  
> Created at: 2019-08-08 17:15:00 UTC  
> Updated at: 2019-08-08 17:24:55 UTC  
> Closed at: 2019-08-08 17:24:55 UTC  
> Url: https://github.com/boostorg/asio/issues/267  

[Sigqueue ](http://man7.org/linux/man-pages/man3/sigqueue.3.html) can be used to send a signal and an accompanying data to a process. Example:  
```  
int main(int argc, char *argv[])  
{  
        union sigval value;  
        int pid = atoi(argv[1]); // process id  
​  
        value.sival_int = 12345; // data  
        if(sigqueue(pid, SIGUSR1, value) == 0) {  
                printf("signal sent successfully!!\n");  
        } else {  
                perror("SIGSENT-ERROR:");  
        }  
        return 0;  
}  
```  
The process can use [sigaction](http://man7.org/linux/man-pages/man2/sigaction.2.html) to get the data sent using `sigqueue`. It needs to set SA_SIGINFO in `sa_flags`. Sample code:  
```  
void sighand(int signo, siginfo_t *info, void *extra)  
{  
       int int_val = info->si_value.sival_int;  
       printf("Signal: %d, value: %d\n", signo, int_val);  
}  
int main()  
{  
        struct sigaction action;  
        action.sa_flags = SA_SIGINFO;  
        action.sa_sigaction = &sighand;  
        if (sigaction(SIGUSR1, &action, NULL) == -1) {  
                perror("sigusr: sigaction");  
                return 0;  
        }  
        printf("Signal handler installed, waiting for signal\n");  
        while(1) {sleep(2);}  
        return 0;  
}  
```  
signal_set seems to be [using](https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/impl/signal_set_service.ipp#L275-L281) sigaction but doesn't set SA_SIGINFO. It will be very useful to have a new API that supports this.

---

## Comment 1

> Username: skannan89  
> Created at: 2019-08-08 17:24:55 UTC  
> Url: https://github.com/boostorg/asio/issues/267#issuecomment-519612566  

Opened an issue here: https://github.com/chriskohlhoff/asio/issues/417 Closing this issue.
