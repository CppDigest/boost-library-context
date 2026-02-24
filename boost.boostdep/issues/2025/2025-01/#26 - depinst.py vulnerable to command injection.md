# #26 - depinst.py vulnerable to command injection [Open]

> Username: andreiminca  
> Created at: 2025-01-16 14:24:59 UTC  
> Updated at: 2025-01-16 14:24:59 UTC  
> Url: https://github.com/boostorg/boostdep/issues/26  

The critical part is how the git_args and modules variables are used to construct the command. If an attacker controls the git_args argument (which can be passed from the command line using -g), they can inject arbitrary shell commands.  
  
### Example of Exploitation  
If an attacker can control the git_args (by passing it via the command-line argument -g), they could inject shell commands like this:  
  
  
`python depinst.py -g "; echo vulnerable; sudo rm -rf /" library_name`  
  
In this case:  
  
git_args would be "; echo vulnerable; sudo rm -rf /".  
The resulting command constructed by the script would be:  
  
`git submodule update --init ; echo vulnerable; sudo rm -rf / libs/module_name  
`  
  
The semicolon (;) separates the git submodule update command from the injected malicious command.  
The injected command echo vulnerable`; sudo rm -rf / `would then be executed after the Git command, leading to destructive actions (in this case, deleting files or running commands with elevated privileges).  
### How to Exploit os.system() in This Context  
**Command Injection through git_args:**  
By providing crafted input for git_args, an attacker can inject arbitrary shell commands. Since os.system() executes the string as a shell command, any malicious input will be executed.  
  
**Privilege Escalation:**  
If the script is run with elevated privileges (e.g., as root or with sudo), the attacker could execute commands with administrative rights, leading to full system compromise.  
  
**Mitigation**  
To mitigate this vulnerability, the following steps should be taken:  
  
**Sanitize Inputs:** Ensure that any input passed to os.system() is sanitized to prevent arbitrary command injection. For example, avoid passing unsanitized arguments directly to shell commands.  
  
**Use subprocess Module:** Rather than using os.system(), it’s safer to use the subprocess module with a list of arguments (avoiding shell interpretation):  
  
  
```  
import subprocess  
subprocess.run(['git', 'submodule', 'update', '--init'] + modules, check=True)  
```  
  
This method avoids invoking the shell and thus prevents command injection.  
  
**Limit the Use of Dynamic Inputs:**   
Avoid constructing commands dynamically with user-supplied input whenever possible. If dynamic inputs are required, be sure to validate and escape them properly. By addressing the dynamic construction of the command with os.system(), the script can be made significantly more secure and resistant to exploitation.
