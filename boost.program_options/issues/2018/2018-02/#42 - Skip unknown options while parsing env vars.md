# #42 - Skip unknown options while parsing env vars [Open]

> Username: ael-code  
> Created at: 2018-02-22 17:20:34 UTC  
> Updated at: 2018-03-28 09:47:13 UTC  
> Url: https://github.com/boostorg/program_options/issues/42  

When using `parse_environment()` It will be convenient to skip environment variables not recognized as program options that still have the specified prefix.  
  
At the moment all the environment variables that start with the specified prefix are collected and a subsequent call to `store()` will raise a `unknown_option` exception

---

## Comment 1

> Username: jeking3  
> Created at: 2018-03-06 14:24:35 UTC  
> Url: https://github.com/boostorg/program_options/issues/42#issuecomment-370797232  

Isn't this situation analogous to passing an unknown option on the command line?    
I'm not certain this needs to change.

---

## Comment 2

> Username: ael-code  
> Created at: 2018-03-27 12:37:23 UTC  
> Updated at: 2018-03-27 12:39:09 UTC  
> Url: https://github.com/boostorg/program_options/issues/42#issuecomment-376509002  

> Isn't this situation analogous to passing an unknown option on the command line?  
> I'm not certain this needs to change.  
  
First of all, unknown options on command line can be allowed using the `basic_command_line_parser` class and the `allow_unregistered` method. At the moment does not exists any way that allows the presence of unregistered variables (with the specified prefix) in the environment.   
  
In any case command line options and environment variables are pretty different things and should be treated as such.  
Usually command lines options are explicitly set and reviewed by the user at each program execution. On the other hand environment variables are generally setup during system startup to set default values. While the set of cli options are specific for the current application, the environment contains variables targeting a wide variety of different application.  
  
The usual approach of command line parsing libraries is to use environment variables as fallback values. If a known command line option have not been provided by the user, the library usually search for the corespondent environment variable in order to get the fallback values. Generally environment variables relative to an unknown options are ignored because they are not fallback of any current supported options.  
  
These are some of the most famous parsing libraries that support unknown environment variables option:  
 - C++:  
   - [gflags](https://gflags.github.io/gflags/): expose a `--tryfromenv` option  
 - Go:  
   - [go-flags](https://godoc.org/github.com/jessevdk/go-flags): implement the fallback approach  
   - [cli](https://github.com/urfave/cli): implement the fallback approach  
 - Python:  
   - [click](http://click.pocoo.org/6/options/#values-from-environment-variables): implement the fallback approach

---

## Comment 3

> Username: eyalroz  
> Created at: 2018-03-27 16:41:03 UTC  
> Updated at: 2018-03-27 16:42:11 UTC  
> Url: https://github.com/boostorg/program_options/issues/42#issuecomment-376592873  

@ael-code : I'm not sure I understand what you're suggesting. You say that:  
> At the moment \[`program_options`\] does not exists any way that allows the presence of unregistered variables (with the specified prefix) in the environment.  
  
I'm looking at the code for [`parse_environment()`](https://github.com/boostorg/program_options/blob/develop/src/parsers.cpp#L189): It matches the options you've specified, and ignores everything else. So, the presence of unregistered environment variables is allowed in the environment, and nothing is thrown. Am I wrong?  
  
Also, I don't see how the second half of your last message relates to your feature request.  
  
(PS - I'm not a developer of the library, just an interested user for now.)

---

## Comment 4

> Username: ael-code  
> Created at: 2018-03-27 17:12:54 UTC  
> Url: https://github.com/boostorg/program_options/issues/42#issuecomment-376603447  

> I'm looking at the code for parse_environment(): It matches the options you've specified, and ignores everything else. So, the presence of unregistered environment variables is allowed in the environment, and nothing is thrown. Am I wrong?  
  
Yeap I think so. The higher level call that you'll use is this one:  https://github.com/boostorg/program_options/blob/646cc20a543017a456ab36b566197be66281a2e1/src/parsers.cpp#L241  
  
It will call that one that you were looking at using  the [prefix_name_mapper](https://github.com/boostorg/program_options/blob/646cc20a543017a456ab36b566197be66281a2e1/src/parsers.cpp#L209) that will match all the environment variables starting with the specified prefix, it will put them in a `parsed_options ` object and it will return it.  
  
The problem pops up when you'll invoke the store function on this object that potentially could have unrecognized options, and.... here you have the exception thrown.

---

## Comment 5

> Username: eyalroz  
> Created at: 2018-03-27 21:09:23 UTC  
> Updated at: 2018-03-27 21:14:07 UTC  
> Url: https://github.com/boostorg/program_options/issues/42#issuecomment-376676229  

I just noticed there's no test case in the library for storing the results of `parse_environment()`...  
  
anyway, ok, now I see what you mean and I agree. Indeed, I think that we should be able configure whether or not to allow unrecognized options with the appropriate prefix in the environment; and the default should be to allow.

---

## Comment 6

> Username: ael-code  
> Created at: 2018-03-28 09:47:13 UTC  
> Url: https://github.com/boostorg/program_options/issues/42#issuecomment-376827499  

> anyway, ok, now I see what you mean and I agree. Indeed, I think that we should be able configure whether or not to allow unrecognized options with the appropriate prefix in the environment; and the default should be to allow.  
  
I definitely agree
