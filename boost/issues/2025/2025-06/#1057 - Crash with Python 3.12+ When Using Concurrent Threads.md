# #1057 - Crash with Python 3.12+ When Using Concurrent Threads [Open]

> Username: jholbrook-kv  
> Created at: 2025-06-24 18:16:06 UTC  
> Updated at: 2025-06-24 18:19:34 UTC  
> Url: https://github.com/boostorg/boost/issues/1057  

Hi, I have a Windows application that has been using Boost Python for many years now, and we are looking to upgrade from Python 3.7 to Python 3.12. This is a C++ application written in Qt5 that uses Boost Python to interface with Python scripts that do some scientific calculations, and the results are displayed in the C++ application. As such, these calculations are by and large done on a concurrent thread so as not to block the UI; however, some quick python functions are run from the main thread.  
  
The problem is that *something* changed in the Python 3.12 upgrade, and I'm not sure what -- but now it crashes when I run Python code from a different thread from the one I called Py_Initialize() on. I've tested up to Python 3.11, and I can Py_Initialize() on the main thread and call Python code from any thread and everything works. But starting in Python 3.12, that's no longer the case. If I initialize Python on the UI thread, I can only run it from that thread. If I initialize it on a concurrent thread, I can only run it on a concurrent thread.  
  
Since I am using Python, Qt5, and Boost Python, I am not 100% sure where the fault lies, but I posted to the Python github and they suggested I post here as well.   
  
I have attached a minimal example project in case that's helpful for debugging: [TestQtBoostPython312.zip](https://github.com/user-attachments/files/20889271/TestQtBoostPython312.zip)  
  
Essentially, this is the meat of my code:  
  
//Initialization Function:  
```C  
Py_Initialize();  
```  
  
//Boost Python Code:  
```C  
boost::python::object main = boost::python::import("__main__");  
boost::python::object name_space(main.attr("__dict__"));  
boost::python::exec_file("testPython.py", name_space, name_space);  
double testNumber = boost::python::extract<double>(name_space["testNumber"]);  
qDebug() << testNumber; //print(testNumber)  
```  
#testPython.py:  
```python  
import random  
testNumber = random.random()  
```  
  
Thanks for any help you can provide.
