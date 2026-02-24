# #386 - SIGSEGV on Ubuntu 22.04 (boost 1.74, python 3.10.4) [Closed]

> Username: glaure  
> Created at: 2022-05-16 13:32:05 UTC  
> Updated at: 2022-05-16 14:15:40 UTC  
> Closed at: 2022-05-16 14:11:48 UTC  
> Url: https://github.com/boostorg/python/issues/386  

Hi we are experiencing a crash when embedding python in our application on **Ubuntu 22.04**:  
  
```  
Program received signal SIGSEGV, Segmentation fault.  
0x00007fab749022c2 in _Py_GetConfig () from /lib/x86_64-linux-gnu/libpython3.10.so.1.0  
(gdb) bt  
#0  0x00007fab749022c2 in _Py_GetConfig () from /lib/x86_64-linux-gnu/libpython3.10.so.1.0  
#1  0x00007fab74826f22 in ?? () from /lib/x86_64-linux-gnu/libpython3.10.so.1.0  
#2  0x0000557f1cfcc953 in _Py_DECREF ()  
#3  0x0000557f1cfcd80d in boost::python::api::object_base::~object_base() ()  
#4  0x0000557f1cfcd6e6 in boost::python::api::object::~object() ()  
#5  0x0000557f1cfcd235 in MyPyInterpreter::deInit() ()  
#6  0x0000557f1cfccb20 in main ()  
```  
  
  
I have extracted a minimum working example reproducing the issue. On other systems the code works:  
  
- Windows 10 (boost 1.74, python 3.7)  
- Ubuntu 20.04 (boost 1.71, python 3.8)  
- RHEL 8 (boost 1.69, python 3.6.8)  
- and many more  
  
The source code to reproduce:  
  
CMakeLists.txt  
```  
cmake_minimum_required(VERSION 3.16.0)  
  
set(CMAKE_CXX_STANDARD 17)  
  
project(py_sigsev_mini)  
  
find_package(PythonLibs REQUIRED)  
  
find_package(Boost     REQUIRED COMPONENTS  
    chrono  
    python  
  )  
  
include_directories(  
    SYSTEM  
    ${Boost_INCLUDE_DIRS}  
    ${PYTHON_INCLUDE_PATH}  
)  
  
add_executable(py_sigsev_mini  
    py_sigsev_mini.cpp  
)  
  
target_link_libraries(py_sigsev_mini  
    Boost::python  
    ${PYTHON_LIBRARIES}  
)  
```  
  
  
 py_sigsev_mini.cpp:  
```C++  
//   
// Mininmum examples crashing on Ubuntu 22.04  
//  
  
#include <boost/python.hpp>  
#include <iostream>  
#include <map>  
#include <string>  
  
  
namespace py = boost::python;  
  
class PyTestModule;  
  
  
class MyPyInterpreter  
{  
public:  
    MyPyInterpreter()  
        : m_main_module()  
        , m_global_ns()  
        , m_module_map()  
        , m_main_thread_state()  
    {  
  
    }  
  
    bool registerModule(PyTestModule* module);  
    bool init();  
    bool deInit();  
    bool exec(const std::string& py_script);  
  
private:  
    boost::python::object  m_main_module;  
    boost::python::object  m_global_ns;  
    typedef std::map<std::string, PyTestModule*> ModuleMap;  
    ModuleMap              m_module_map;  
    PyThreadState*         m_main_thread_state;  
};  
  
  
void testMessage()  
{  
    std::cerr << "testMessage" << std::endl;  
}  
  
/**  
 * Create the "PyTestModule"  
 */  
BOOST_PYTHON_MODULE(PyTestModule)  
{  
    py::def("testMessage", &testMessage);  
  
}  
  
  
class PyTestModule  
{  
public:  
    PyTestModule()  
    {}  
    virtual ~PyTestModule()  
    {}  
  
    virtual std::string getName() const  
    {  
        return "PyTestModule";  
    }  
  
    virtual bool appendInittab()  
    {  
        return -1 != PyImport_AppendInittab("PyTestModule", &PyInit_PyTestModule);  
    }  
};  
  
  
int main()  
{  
    MyPyInterpreter p;  
  
    p.registerModule(new PyTestModule);  
    p.init();  
  
    // call Function  
    p.exec("PyTestModule.testMessage()");  
  
    p.deInit();     
  
    return 0;  
}  
  
  
bool MyPyInterpreter::registerModule(PyTestModule* module)  
{  
    return m_module_map.emplace(module->getName(), std::move(module)).second;  
}  
  
bool MyPyInterpreter::init()  
{  
    // preload extensions  
    for (const auto& m : m_module_map)  
    {  
        if (!m.second->appendInittab())  
        {  
            return false;  
        }  
    }  
  
    Py_Initialize();  
  
    // Retrieve the main module  
    m_main_module = py::import("__main__");  
  
    // Retrieve the main module's namespace  
    m_global_ns   = m_main_module.attr("__dict__");  
  
    // Auto - import the applications python modules  
    for (const auto& m : m_module_map)  
    {  
        std::string name = m.second->getName();  
        py::object module = py::import(name.c_str());  
        m_global_ns[name.c_str()] = module;  
    }  
  
    // Release GIL  
    m_main_thread_state = PyEval_SaveThread();  
  
    return true;  
}  
  
bool MyPyInterpreter::deInit()  
{  
    if (Py_IsInitialized())  
    {  
        PyEval_RestoreThread(m_main_thread_state);  
  
        // unregister modules  
        py::object sys = py::import("sys");  
        for (const auto& m : m_module_map)  
        {  
            std::string name = m.second->getName();  
            try  
            {  
                sys.attr("modules")[name.c_str()].del();  
            }  
            catch(py::error_already_set&)  
            {  
            }  
        }  
        m_module_map.clear();  
  
        m_global_ns   = py::object();  
        m_main_module = py::object();  
  
        Py_Finalize();  
    }  
    return true;  
}  
  
bool MyPyInterpreter::exec(const std::string& py_script)  
{  
    auto gil = PyGILState_Ensure();  
    py::exec(py_script.c_str(), m_global_ns);  
    PyGILState_Release(gil);  
    return true;  
}  
```  
  
  
Best regards,  
  
Gunther

---

## Comment 1

> Username: glaure  
> Created at: 2022-05-16 14:11:43 UTC  
> Updated at: 2022-05-16 14:15:40 UTC  
> Url: https://github.com/boostorg/python/issues/386#issuecomment-1127727211  

I found the issue:  
  
py::object sys = py::import("sys");  
  
is the culprit. It has to be set to py::object()  before calling Py_Finalize();
