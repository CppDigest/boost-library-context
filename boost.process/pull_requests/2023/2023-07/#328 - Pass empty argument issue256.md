# #328 Pass empty argument issue256 [Merged]

> Username: SilverPlate3  
> Created at: 2023-07-05 17:16:18 UTC  
> Updated at: 2023-08-14 09:45:01 UTC  
> Merged at: 2023-08-14 09:45:00 UTC  
> Closed at: 2023-08-14 09:45:00 UTC  
> Url: https://github.com/boostorg/process/pull/328  

### Fixing issue: https://github.com/boostorg/process/issues/256  
At the moment, we can't pass an empty argument "" to the child. This is an issue for programs that may take an empty string as an argument.   
**This limitations only occurs on windows!**  
  
For example a string replacement program. You might want to replace "XYZ" with "" (thus deleting the unwanted string).  
In the above issue, the user needed it for the ImageMagic app.  
  
I created a simple program that takes arguments via CMD and prints them  
```  
int main(int argc, char* argv[]) {  
    std::cout << "Number of arguments: " << argc - 1 << std::endl;  
    for (int i = 1; i < argc; ++i) {  
        std::cout << "Argument " << i << ": " << argv[i] << std::endl;  
    }  
    std::cout << "\n" << std::endl;  
}  
```  
Running the program with an empty argument via cmd:  
![image](https://github.com/boostorg/process/assets/93097769/3adb567d-1e78-4b11-b18d-151109d571e4)  
  
Current boost::process implementation:  
When I try to pass an empty string as an argument, it gets "thrown" and isn't passed properly to the child program.   
![image](https://github.com/boostorg/process/assets/93097769/21563f92-8808-4f94-aed1-2e3980d86dd6)  
![image](https://github.com/boostorg/process/assets/93097769/1b21af3a-ede5-48ef-bc6b-57cfc489eaef)  
  
  
After changing the code so it will pass empty arguments properly:  
![image](https://github.com/boostorg/process/assets/93097769/739b2c9e-dfc1-4754-b2cc-06a2afc66d3e)  
  
  
Good thing is that looks like we can't pass an un-intended empty argument (we must specify ""). Or at least I wasn't able to reproduce such edge case.

---

## Comment 1

> Username: SilverPlate3  
> Created_at: 2023-07-08 10:33:11 UTC  
> Url: https://github.com/boostorg/process/pull/328#issuecomment-1627082135  

@klemens-morgenstern  
When will this fix be integrated?  
I think it will be nice if merged before next Boost release.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2023-07-08 11:06:25 UTC  
> Url: https://github.com/boostorg/process/pull/328#issuecomment-1627120799  

Master branch is locked for thebcurrent releasr atm.

---
