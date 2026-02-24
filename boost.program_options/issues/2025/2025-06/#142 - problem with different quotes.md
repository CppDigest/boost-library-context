# #142 - problem with different quotes [Open]

> Username: jean-christophe81  
> Created at: 2025-06-20 07:57:40 UTC  
> Updated at: 2025-06-23 07:33:41 UTC  
> Url: https://github.com/boostorg/program_options/issues/142  

boost 1.87  
alma9  
  
When you mix " and ' in a command line unix command line, unix parser and perhaps  windows one doesn't take into account the first type of quote.  
An example:  
with this command line: /bin/sh -c '/bin/echo "ResourceFile AdminPager: " >> /tmp/notif_toto.txt'  
  
It split in  
std::vector of length 5, capacity 8 = {"/bin/sh", "-c", "/bin/echo ResourceFile",  "AdminPager:", " >> /tmp/notif_toto.txt"}  
  
It should split in:  
std::vector of length 3, capacity 8 = {"/bin/sh", "-c", "/bin/echo "ResourceFile AdminPager:" >> /tmp/notif_toto.txt"}  
  
An example of implementation: [centreon collect](https://github.com/centreon/centreon-collect/blob/4fc9ec16970fa0390aa2e185040ad47cfd0a852c/clib/src/misc/command_line.cc#L136)
