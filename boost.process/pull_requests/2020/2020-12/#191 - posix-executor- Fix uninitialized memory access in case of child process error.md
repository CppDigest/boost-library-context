# #191 posix/executor: Fix uninitialized memory access in case of child process error [Closed]

> Username: dkl  
> Created at: 2020-12-12 22:04:22 UTC  
> Updated at: 2021-10-15 18:01:42 UTC  
> Closed at: 2021-10-15 04:39:52 UTC  
> Url: https://github.com/boostorg/process/pull/191  

The parent process didn't validate the amount of bytes received from the child process, so it sometimes accessed uninitialized memory. This could be triggered by error cases using the old write_error() function, which sends 4 + 4 + N bytes instead of 8 + N as assumed by _read_error().  
  
For example: Giving an invalid file name for stdout/stdin redirection (or if the file cannot be opened for some other reason):  
```  
std::string empty_filename;  
bp::child c(argv, bp::std_out > empty_filename);  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2021-10-14 06:22:55 UTC  
> Url: https://github.com/boostorg/process/pull/191#issuecomment-943006008  

I don't understand why this would cause an error? Wouldn't the buffer of the pipe avoid that?

---

## Comment 2

> Username: dkl  
> Created_at: 2021-10-14 18:30:18 UTC  
> Url: https://github.com/boostorg/process/pull/191#issuecomment-943613873  

It comes down to this: `read()` can return less bytes than requested. It works like that for files, sockets, and apparently even for pipes. Because of that, it may need to be called repeatedly to get the total amount of bytes wanted.  
  
What I saw here was the parent process wanting to read 8 bytes from the pipe, but (sometimes) receiving only 4 bytes in the first read() call. In that case it required a second read() call to read the remaining 4 bytes. Since it didn't do that, it used uninitialized memory in the buffer instead.  
  
It seemed to happen only if the child process hit that one case which uses `write(fd, buffer1, 4); write(fd, buffer2, 4);` (in the old `write_error()` function) instead of `write(fd, buffer, 8)` (in the old `_write_error()` function). However, chances are that's not the whole explanation. It looks like the pipe can (and sometimes will) merge the two 4-byte writes, such that read() will return 8 bytes at once as if 8 bytes were written at once, but it's just that this behaviour is not guaranteed. So there probably is a race condition too.  
  
To reproduce both cases, the following demo program using pipe/fork/read/write seems to do the trick here. It has a child process doing the two write() calls writing 4 bytes each, and there is a `sleep(1);` call between the two writes in the child process.   
```  
#include <fcntl.h>  
#include <stdio.h>  
#include <sys/types.h>  
#include <sys/wait.h>  
#include <unistd.h>  
  
int main() {  
	int res;  
	int pipefd[2];  
  
	res = pipe(pipefd);  
	if (res < 0) {  
		perror("pipe");  
		return 1;  
	}  
  
	pid_t child = fork();  
  
	if (child == -1) {  
		perror("fork");  
		return 1;  
	}  
  
	if (child == 0) {  
		// child process  
  
		close(pipefd[0]); // close read fd  
		pipefd[0] = -1;  
  
		{  
			char buffer[4] = {0, 1, 2, 3};  
			printf("child: write(%zu) = ...\n", sizeof(buffer));  
			ssize_t byteswritten = write(pipefd[1], buffer, sizeof(buffer));  
			if (byteswritten < 0) {  
				perror("write");  
				return 1;  
			}  
			printf("child: write(%zu) = %zu\n", sizeof(buffer), (size_t)byteswritten);  
		}  
  
  
		// try with or without this, and check whether you see a difference in the output  
		sleep(1);  
  
		{  
			char buffer[4] = {4, 5, 6, 7};  
			printf("child: write(%zu) = ...\n", sizeof(buffer));  
			ssize_t byteswritten = write(pipefd[1], buffer, sizeof(buffer));  
			if (byteswritten < 0) {  
				perror("write");  
				return 1;  
			}  
			printf("child: write(%zu) = %zu\n", sizeof(buffer), (size_t)byteswritten);  
		}  
  
		close(pipefd[1]); // close write fd  
		pipefd[1] = -1;  
		return 0;  
	}  
  
	// parent process  
  
	close(pipefd[1]); // close write fd  
	pipefd[1] = -1;  
  
	while (1) {  
		char buffer[8];  
		printf("parent: read(%zu) = ...\n", sizeof(buffer));  
		ssize_t bytesread = read(pipefd[0], buffer, sizeof(buffer));  
		if (bytesread < 0) {  
			perror("read");  
			return 1;  
		}  
		printf("parent: read(%zu) = %zu\n", sizeof(buffer), (size_t)bytesread);  
		if (bytesread == 0) {  
			break;  
		}  
	}  
  
	close(pipefd[0]); // close read fd  
	pipefd[0] = -1;  
  
	int status = 0;  
	pid_t result = waitpid(child, &status, 0);  
	if (result == -1) {  
		perror("waitpid");  
		return 1;  
	}  
  
	return 0;  
}  
```  
Without the sleep, the parent reads 8 bytes immediately here (lucky timing I guess):  
```  
parent: read(8) = ...  
child: write(4) = ...  
child: write(4) = 4  
child: write(4) = ...  
child: write(4) = 4  
parent: read(8) = 8  
parent: read(8) = ...  
parent: read(8) = 0  
```  
Different behaviour with the sleep:  
```  
parent: read(8) = ...  
child: write(4) = ...  
child: write(4) = 4  
parent: read(8) = 4  
parent: read(8) = ...  
child: write(4) = ...  
child: write(4) = 4  
parent: read(8) = 4  
parent: read(8) = ...  
parent: read(8) = 0  
```

---

## Comment 3

> Username: dkl  
> Created_at: 2021-10-15 18:01:42 UTC  
> Url: https://github.com/boostorg/process/pull/191#issuecomment-944493205  

Thanks for the fix, I think this one was important. Of course I'd prefer to check the read() return value, but I think your solution will solve the issue in practice, too. Time to test it a bit.

---
