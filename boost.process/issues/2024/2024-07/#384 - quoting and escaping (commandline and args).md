# #384 - quoting and escaping (commandline and args) [Open]

> Username: user706  
> Created at: 2024-07-10 07:10:37 UTC  
> Updated at: 2024-07-10 08:38:15 UTC  
> Url: https://github.com/boostorg/process/issues/384  

Hi,  
  
how can one get the quoting and escaping right, when one wants to issue complex command-lines?  
  
Here are examples:  
  
a)  
```bash  
/bin/bash -c 'set -Eeuo pipefail ; trap "trap - SIGTERM && echo \"killing -$$\" && kill -- -$$" SIGINT SIGTERM EXIT; echo "pid $$" ; sleep 1; echo "done"'  
```  
  
equiv with the following (but now using only double-quotes)  
  
b)  
```bash  
/bin/bash -c "set -Eeuo pipefail ; trap \"trap - SIGTERM && echo \\\"killing -\$\$\\\" && kill -- -\$\$\" SIGINT SIGTERM EXIT; echo \"pid \$\$\" ; sleep 1; echo \"done\""  
```  
  
equiv with  
  
a2)  
```bash  
eval "/bin/bash -c 'set -Eeuo pipefail ; trap \"trap - SIGTERM && echo \\\"killing -\$\$\\\" && kill -- -\$\$\" SIGINT SIGTERM EXIT; echo \"pid \$\$\" ; sleep 1; echo \"done\"'"  
```  
  
equiv with  
  
b2)  
```bash  
eval "/bin/bash -c \"set -Eeuo pipefail ; trap \\\"trap - SIGTERM && echo \\\\\\\"killing -\\\$\\\$\\\\\\\" && kill -- -\\\$\\\$\\\" SIGINT SIGTERM EXIT; echo \\\"pid \\\$\\\$\\\" ; sleep 1; echo \\\"done\\\"\""  
```  
  
**How does one construct those command-lines above, for boost.process??**  
  
If I copy/paste the above commands in bash, I get this output:  
```cpp  
pid 20906  
done  
killing -20906  
Terminated               // this line (and line above this one) is printed because of EXIT in the command (removing EXIT, will result in TERMINATED not being printed  
```  
  
More easily readable, I basically want to have `/bin/bash` execute the following:  
  
```bash  
set -Eeuo pipefail  
trap "trap - SIGTERM && echo \"killing -$$\" && kill -- -$$" SIGINT SIGTERM EXIT  
echo "pid $$"  
sleep 1  
echo "done"  
```  
  
  
Thanks!

---

## Comment 1

> Username: user706  
> Created at: 2024-07-10 08:29:19 UTC  
> Updated at: 2024-07-10 08:38:15 UTC  
> Url: https://github.com/boostorg/process/issues/384#issuecomment-2219882419  

Perhaps like this:  
  
```cpp  
#include <boost/process.hpp>  
  
#include <string>  
#include <iostream>  
#include <chrono>  
#include <thread>  
  
int main()  
{  
    namespace bp = boost::process;  
    bp::group g;  
//  bp::child c(        "/bin/bash",           "-c",  "set -Eeuo pipefail ; trap \"trap - SIGTERM && echo \\\"killing -$$\\\" && kill -- -$$\" SIGINT SIGTERM EXIT; echo \"pid $$\" ; sleep 1; echo \"done\"" , g);  
    bp::child c(bp::exe="/bin/bash", bp::args={"-c",  "set -Eeuo pipefail ; trap \"trap - SIGTERM && echo \\\"killing -$$\\\" && kill -- -$$\" SIGINT SIGTERM EXIT; echo \"pid $$\" ; sleep 1; echo \"done\""}, g);  
    //                                                ^^^^^^^...  
    //                                                same as b) above; except: here we do not escape $:   so instead of \$   we use $  
      
    std::cout << "\tNote: c.id() == " << c.id() << std::endl;  
      
    std::this_thread::sleep_for(std::chrono::milliseconds{100});  
    //::kill(-c.id(), SIGTERM);  
    //::kill(-c.id(), SIGINT);  
      
    c.wait();  
}  
```  
  
How would one do it in a single string (instead of splitting into `"/bin/bash"`, `"-c"`,  `"..."`) ? Is that possible?
