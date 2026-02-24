# #754 - build error on Raspberry Pi 2 Model B [Closed]

> Username: LaySoft  
> Created at: 2022-02-22 14:08:55 UTC  
> Updated at: 2022-02-22 14:09:55 UTC  
> Closed at: 2022-02-22 14:09:55 UTC  
> Url: https://github.com/boostorg/build/issues/754  

I tried to install openswoole php pecl package on my Raspberry (Ubuntu 21.10) with the command:  
  
pecl install openswoole  
  
After a long time it breaks with the error:  
  
libtool: compile: cc -I. -I/tmp/pear/temp/openswoole -I/tmp/pear/temp/pear-build-root6fqqK8/openswoole-4.10.0/include -I/tmp/pear/temp/pear-build-root6fqqK8/openswoole-4.10.0/main -I/tmp/pear/temp/openswoole -I/usr/include/php/20200930 -I/usr/include/php/20200930/main -I/usr/include/php/20200930/TSRM -I/usr/include/php/20200930/Zend -I/usr/include/php/20200930/ext -I/usr/include/php/20200930/ext/date/lib -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64 -I/tmp/pear/temp/openswoole -I/tmp/pear/temp/openswoole/include -I/tmp/pear/temp/openswoole/ext-src -I/tmp/pear/temp/openswoole/thirdparty/hiredis -DHAVE_CONFIG_H -Wall -pthread -g -O2 -DENABLE_PHP_SWOOLE -c /tmp/pear/temp/openswoole/thirdparty/boost/asm/jump_arm_aapcs_elf_gas.S -fPIC -DPIC -o thirdparty/boost/asm/.libs/jump_arm_aapcs_elf_gas.o  
/tmp/pear/temp/openswoole/thirdparty/boost/asm/jump_arm_aapcs_elf_gas.S: Assembler messages:  
/tmp/pear/temp/openswoole/thirdparty/boost/asm/jump_arm_aapcs_elf_gas.S:60: Error: selected processor does not support vstmia sp,{d8-d15}' in ARM mode /tmp/pear/temp/openswoole/thirdparty/boost/asm/jump_arm_aapcs_elf_gas.S:77: Error: selected processor does not support vldmia sp,{d8-d15}' in ARM mode  
make: *** [Makefile:463: thirdparty/boost/asm/jump_arm_aapcs_elf_gas.lo] Error 1  
ERROR: `make' failed  
  
It seems it's a boostorg problem: https://github.com/boostorg/context/issues/114  
  
Is there any way, to fix this?

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2022-02-22 14:09:54 UTC  
> Url: https://github.com/boostorg/build/issues/754#issuecomment-1047834606  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
