# #569 - boost 1.72 can't build in ubuntu 18.04 [Closed]

> Username: wanggujin  
> Created at: 2020-04-18 11:19:15 UTC  
> Updated at: 2020-04-18 17:59:55 UTC  
> Closed at: 2020-04-18 17:59:55 UTC  
> Url: https://github.com/boostorg/build/issues/569  

I download boost 1.72.0 today, but i can't build it in ubuntu 18.04.  
./bootstrap.sh  failed.  
bootstrap.log  as follow:  
g++ --version  
g++ (Ubuntu 7.5.0-3ubuntu1~18.04) 7.5.0  
Copyright (C) 2017 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
###  
###  
g++ -x c++ -std=c++11 -O2 -s -DNDEBUG builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp filesys.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam.cpp jambase.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathsys.cpp regexp.cpp rules.cpp scan.cpp search.cpp strings.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp execunix.cpp fileunix.cpp pathunix.cpp -o b2  
debugger.cpp: In function ‘void debug_mi_format_breakpoint(int)’:  
debugger.cpp:1837:16: warning: zero-length gnu_printf format string [-Wformat-zero-length]  
     printf( "" );  
                ^  
debugger.cpp: In function ‘LIST* debug_list_read(FILE*)’:  
debugger.cpp:165:11: warning: ignoring return value of ‘int fscanf(FILE*, const char*, ...)’, declared with attribute warn_unused_result [-Wunused-result]  
     fscanf( in, "%d", &len );  
     ~~~~~~^~~~~~~~~~~~~~~~~~  
/tmp/ccm9uJWA.o: In function `class_module_name(_object*)':  
class.cpp:(.text+0x30): undefined reference to `string_new(string*)'  
class.cpp:(.text+0x3f): undefined reference to `string_append(string*, char const*)'  
class.cpp:(.text+0x4a): undefined reference to `string_append(string*, char const*)'  
class.cpp:(.text+0x5e): undefined reference to `string_free(string*)'  
/tmp/ccm9uJWA.o: In function `import_base_rule(void*, void*)':  
class.cpp:(.text+0xb9): undefined reference to `string_new(string*)'  
class.cpp:(.text+0xc5): undefined reference to `string_append(string*, char const*)'  
class.cpp:(.text+0xd2): undefined reference to `string_push_back(string*, char)'  
class.cpp:(.text+0xdd): undefined reference to `string_append(string*, char const*)'  
class.cpp:(.text+0xf1): undefined reference to `string_free(string*)'  
/tmp/ccvmaKU5.o: In function `make_absolute_path(_object*)':  
debugger.cpp:(.text+0x7a1): undefined reference to `string_new(string*)'  
debugger.cpp:(.text+0x7c0): undefined reference to `string_free(string*)'  
/tmp/ccvmaKU5.o: In function `get_filename(_object*)':  
debugger.cpp:(.text+0x83c): undefined reference to `string_new(string*)'  
debugger.cpp:(.text+0x85b): undefined reference to `string_free(string*)'  
/tmp/ccvmaKU5.o: In function `debug_object_read(_IO_FILE*)':  
debugger.cpp:(.text+0x8b0): undefined reference to `string_new(string*)'  
debugger.cpp:(.text+0x8c7): undefined reference to `string_push_back(string*, char)'  
debugger.cpp:(.text+0x8e7): undefined reference to `string_free(string*)'  
/tmp/ccvmaKU5.o: In function `debug_frame_read(_IO_FILE*, _frame_info*)':  
debugger.cpp:(.text+0xa9a): undefined reference to `string_new(string*)'  
debugger.cpp:(.text+0xaa7): undefined reference to `string_push_back(string*, char)'  
debugger.cpp:(.text+0xac7): undefined reference to `string_free(string*)'  
/tmp/ccvmaKU5.o: In function `debug_child_print(int, char const**)':  
debugger.cpp:(.text+0xf75): undefined reference to `string_new(string*)'  
debugger.cpp:(.text+0xf84): undefined reference to `string_append(string*, char const*)'  
debugger.cpp:(.text+0xfad): undefined reference to `string_push_back(string*, char)'  
debugger.cpp:(.text+0xfb9): undefined reference to `string_append(string*, char const*)'  
debugger.cpp:(.text+0xfcd): undefined reference to `string_append(string*, char const*)'  
debugger.cpp:(.text+0xffe): undefined reference to `string_free(string*)'  
/tmp/ccvmaKU5.o: In function `read_command()':  
debugger.cpp:(.text+0x151d): undefined reference to `string_new(string*)'  
debugger.cpp:(.text+0x152a): undefined reference to `string_reserve(string*, unsigned long)'  
debugger.cpp:(.text+0x153c): undefined reference to `string_push_back(string*, char)'  
debugger.cpp:(.text+0x1560): undefined reference to `string_free(string*)'  
/tmp/ccvmaKU5.o: In function `debug_child_info(int, char const**)':  
debugger.cpp:(.text+0x2ede): undefined reference to `string_copy(string*, char const*)'  
debugger.cpp:(.text+0x2eeb): undefined reference to `string_push_back(string*, char)'  
debugger.cpp:(.text+0x2efb): undefined reference to `string_append(string*, char const*)'  
debugger.cpp:(.text+0x2f6e): undefined reference to `string_free(string*)'  
debugger.cpp:(.text+0x302f): undefined reference to `string_copy(string*, char const*)'  
/tmp/ccvmaKU5.o: In function `debug_mi_stack_list_locals(int, char const**)':  
debugger.cpp:(.text+0x3288): undefined reference to `string_free(string*)'  
debugger.cpp:(.text+0x32e5): undefined reference to `string_new(string*)'  
debugger.cpp:(.text+0x32f4): undefined reference to `string_append(string*, char const*)'  
debugger.cpp:(.text+0x32ff): undefined reference to `string_append(string*, char const*)'  
debugger.cpp:(.text+0x330e): undefined reference to `string_append(string*, char const*)'  
/tmp/ccvmaKU5.o: In function `debug_mi_stack_list_variables(int, char const**)':  
debugger.cpp:(.text+0x3648): undefined reference to `string_free(string*)'  
debugger.cpp:(.text+0x36a5): undefined reference to `string_new(string*)'  
debugger.cpp:(.text+0x36b4): undefined reference to `string_append(string*, char const*)'  
debugger.cpp:(.text+0x36bf): undefined reference to `string_append(string*, char const*)'  
debugger.cpp:(.text+0x36ce): undefined reference to `string_append(string*, char const*)'  
/tmp/cc2eUgAy.o: In function `property_set_create(frame*, int)':  
property-set.cpp:(.text+0x55d): undefined reference to `string_new(string*)'  
property-set.cpp:(.text+0x56c): undefined reference to `string_append(string*, char const*)'  
property-set.cpp:(.text+0x577): undefined reference to `string_append(string*, char const*)'  
property-set.cpp:(.text+0x586): undefined reference to `string_append(string*, char const*)'  
property-set.cpp:(.text+0x5c1): undefined reference to `string_free(string*)'  
/tmp/ccYK2X2Q.o: In function `regex_replace(frame*, int)':  
regex.cpp:(.text+0xe7): undefined reference to `string_new(string*)'  
regex.cpp:(.text+0xfb): undefined reference to `string_append_range(string*, char const*, char const*)'  
regex.cpp:(.text+0x106): undefined reference to `string_append(string*, char const*)'  
regex.cpp:(.text+0x134): undefined reference to `string_append(string*, char const*)'  
regex.cpp:(.text+0x150): undefined reference to `string_free(string*)'  
regex.cpp:(.text+0x180): undefined reference to `string_push_back(string*, char)'  
/tmp/ccYK2X2Q.o: In function `regex_transform(frame*, int)':  
regex.cpp:(.text+0x298): undefined reference to `string_new(string*)'  
regex.cpp:(.text+0x308): undefined reference to `string_append_range(string*, char const*, char const*)'  
regex.cpp:(.text+0x32a): undefined reference to `string_truncate(string*, unsigned long)'  
regex.cpp:(.text+0x346): undefined reference to `string_free(string*)'  
/tmp/ccdy5xkp.o: In function `path_translate_to_os_(char const*, string*)':  
pathunix.cpp:(.text+0x4e): undefined reference to `string_copy(string*, char const*)'  
/tmp/ccdy5xkp.o: In function `path_get_temp_path_(string*)':  
pathunix.cpp:(.text+0x36): undefined reference to `string_append(string*, char const*)'  
collect2: error: ld returned 1 exit status  
  
Someone can help?

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-04-18 17:59:55 UTC  
> Url: https://github.com/boostorg/build/issues/569#issuecomment-615913974  

Duplicate of https://github.com/boostorg/build/issues/468
