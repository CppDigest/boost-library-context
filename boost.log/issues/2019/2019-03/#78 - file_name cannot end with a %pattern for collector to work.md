# #78 - file_name cannot end with a %pattern for collector to work [Closed]

> Username: pulver  
> Created at: 2019-03-30 18:53:26 UTC  
> Updated at: 2019-04-14 17:36:34 UTC  
> Closed at: 2019-04-14 17:36:03 UTC  
> Url: https://github.com/boostorg/log/issues/78  

When initializing a `sinks::text_file_backend`, an attached `collector` won't delete files that exceed `max_files` unless the `file_name` ends with a static (non-pattern) character.  
  
This works fine:  
`keywords::file_name = "logs/file.%Y%m%d-%H%M%S.log"`  
but this doesn't:  
`keywords::file_name = "logs/file.%Y%m%d-%H%M%S"`  
in that any number of files in the second case can exist, beyond the limit set by the `collector`'s `max_files`.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-03-31 00:49:52 UTC  
> Url: https://github.com/boostorg/log/issues/78#issuecomment-478301865  

I can't reproduce it with `example/rotating_file`. Can you provide a small compilable code sample that shows the problem?

---

## Comment 2

> Username: pulver  
> Created at: 2019-03-31 12:32:42 UTC  
> Url: https://github.com/boostorg/log/issues/78#issuecomment-478337720  

1. Start with `boost_1_70_0/libs/log/example/doc/sinks_xml_file.cpp`.  
2. Change `keywords::max_files = 512` to `keywords::max_files = 3`.  
3. Compile and run: `g++ sinks_xml_file.cpp -lboost_log -lboost_thread -lpthread -lboost_filesystem -DBOOST_LOG_DYN_LINK ; ./a.out ; ls logs`  
4. 3 files show up, as expected: `20190331_082025_00008.xml  20190331_082025_00009.xml  20190331_082025_00010.xml`  
5. Run `./a.out` again. 3 files are in `logs` and old ones were deleted as expected.  
5. `rm -Rf logs`  
  
1. Change `keywords::file_name = "%Y%m%d_%H%M%S_%5N.xml"` to `keywords::file_name = "%Y%m%d_%H%M%S_%5N"`  
2. Compile and run.  
3. 3 files show up again, as expected: `20190331_082132_00008 20190331_082132_00009 20190331_082132_00010`  
4. Run `./a.out` again. 6 files are in `logs`. 3 are expected.  
5. Run `./a.out` again. 9 files are in `logs`. 3 are expected.  
6. ...

---

## Comment 3

> Username: Lastique  
> Created at: 2019-04-14 17:36:34 UTC  
> Url: https://github.com/boostorg/log/issues/78#issuecomment-483025077  

Should be fixed in d7a87119e683081ed9daca4d2f83c1afeacbc77a, thanks.
