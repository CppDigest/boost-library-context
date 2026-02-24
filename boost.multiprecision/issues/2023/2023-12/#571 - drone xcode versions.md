# #571 - drone xcode versions [Closed]

> Username: sdarwin  
> Created at: 2023-12-01 21:02:18 UTC  
> Updated at: 2023-12-05 09:16:45 UTC  
> Closed at: 2023-12-05 09:16:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/571  

The oldest drone MacOS machines in production, High Sierra:  
  
```  
dronerunnermac2:~$ sw_vers   
ProductName:	Mac OS X  
ProductVersion:	10.13.6  
BuildVersion:	17G14042  
```  
  
Xcode versions 6 through 9.4.1.  Those machines are nearly always idle. Reviewing boostorg libraries, multiprecision is running tests with these versions.       
  
2 * macmini at macminivault.com  
  
Perhaps we could shut them down, and upgrade?   Update the .drone.star tests.  This week I deployed 1 MacOS Sonoma, Xcodes versions available: 14.2.0 14.3.0 14.3.1 15.0.0 15.0.1 .

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-12-02 08:47:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/571#issuecomment-1837091049  

Yes by all means, these CI tests get ossified after a while.
