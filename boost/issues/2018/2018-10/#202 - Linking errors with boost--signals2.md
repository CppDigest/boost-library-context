# #202 - Linking errors with boost::signals2 [Closed]

> Username: ragoston  
> Created at: 2018-10-06 09:48:13 UTC  
> Updated at: 2019-06-26 15:11:03 UTC  
> Closed at: 2019-06-26 15:11:02 UTC  
> Url: https://github.com/boostorg/boost/issues/202  

I have a class `Window` that has 3 static events:  
```  
  
class Window  
{  
public:  
	static boost::signals2::signal<void(GLFWwindow*, double, double)> event_MouseMovementCallback;  
	static boost::signals2::signal<void(GLFWwindow*, double, double)> event_ScrollCallback;  
	static boost::signals2::signal<void(GLFWwindow*, int, int, int)> event_MouseButtonCallback;  
```  
  
The mere presence of these events break the linking stage of the application with error messages:  
  
> error LNK2001: unresolved external symbol "public: static class boost::signals2::signal<void __cdecl(struct GLFWwindow *,double,double),class boost::signals2::optional_last_value<void>,int,struct std::less<int>,class boost::function<void __cdecl(struct GLFWwindow *,double,double)>,class boost::function<void __cdecl(class boost::signals2::connection const &,struct GLFWwindow *,double,double)>,class boost::signals2::mutex> Window::event_MouseMovementCallback" (?event_MouseMovementCallback@Window@@2V?$signal@$$A6AXPAUGLFWwindow@@NN@ZV?$optional_last_value@X@signals2@boost@@HU?$less@H@std@@V?$function@$$A6AXPAUGLFWwindow@@NN@Z@4@V?$function@$$A6AXABVconnection@signals2@boost@@PAUGLFWwindow@@NN@Z@4@Vmutex@34@@signals2@boost@@A)  
  
I get this error for all three of them (so basically 3 errors of the same kind). Note that commenting out the usages of these event changes nothing, but commenting out the events themselves solves the problem so it is highly probable that these definitions cause the issue.   
  
I got boost from your site as a zip, unpacked and compiled with boost.build as per the instructions on your site. I used msvc 14.1 using Visual Studio 2017.   
I tried this with precompiled binaries but got the same result.   
After building I set the VC++ dirs in the project properties to find the boost headers and the libs.   
It didn't work so I tried setting the additional libraries in the Linker too but to no avail.   
  
My project is header only and uses opengl and assimp.   
  
As far as I know boost::signals2 is header only which is why I wonder why I got these errors and how to get rid of them.   
  
Thanks for your help in advance.

---

## Comment 1

> Username: mclow  
> Created at: 2018-10-06 14:07:01 UTC  
> Url: https://github.com/boostorg/boost/issues/202#issuecomment-427576424  

A better place for this report is on the signals2 page: https://github.com/boostorg/signals2

---

## Comment 2

> Username: mjcaisse  
> Created at: 2019-06-26 15:11:02 UTC  
> Url: https://github.com/boostorg/boost/issues/202#issuecomment-505918744  

Assume issue has been moved to signals2 tracker.
