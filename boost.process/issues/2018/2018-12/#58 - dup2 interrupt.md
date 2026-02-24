# #58 - dup2 interrupt [Closed]

> Username: ghost  
> Created at: 2018-12-01 09:47:10 UTC  
> Updated at: 2018-12-10 07:46:35 UTC  
> Closed at: 2018-12-10 07:46:35 UTC  
> Url: https://github.com/boostorg/process/issues/58  

Hi All,  
  
In the latest code of boost::process the function dup2() is called as follows, e.g.:  
if (::dup2(sink, STDERR_FILENO) == -1) ....  
  
Could it be an issue if interrupt had happened? Some people recommend the following way, e.g.:  
while ((dup2(filedes[1], STDOUT_FILENO) == -1) && (errno == EINTR)) {}    
( http://www.microhowto.info/howto/capture_the_output_of_a_child_process_in_c.html )

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-12-01 10:02:50 UTC  
> Url: https://github.com/boostorg/process/issues/58#issuecomment-443414859  

It'll lead to an error and I thin that's the proper way to handle it. I.e. it'll appear in the `error_code` or the exception. I don't think just repeating after an interrupt should be the standard solution.
