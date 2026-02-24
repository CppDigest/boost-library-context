# #383 - exec_file crash on Debug mode (Win 10) [Open]

> Username: mehdioa  
> Created at: 2022-03-03 09:10:16 UTC  
> Updated at: 2022-03-05 12:20:04 UTC  
> Url: https://github.com/boostorg/python/issues/383  

I face a crash on build 1.78 of boost python, on Windows 10, VS2019, VC142, Python 3.10 only on **debug** mode. Here is the script  
  
```  
#ifdef _DEBUG  
  
#undef _DEBUG  
  
#endif  
#define PY_SSIZE_T_CLEAN  
#include <Python.h>  
int main(int argc, char* argv[])  
{  
	Py_Initialize();  
	char* f = const_cast<char*>("E:\\Projects\\TestBoostPython\\embedding.py");  
	PyObject* fo = Py_BuildValue("s", f);  
	PyObject* fb = Py_None;  
	PyUnicode_FSConverter(fo, &fb);  
	char* f_as_uft = PyBytes_AsString(fb);  
	FILE* fs = fopen(f_as_uft, "r+");  
	Py_DECREF(fo);  
	Py_DECREF(fb);  
	PyObject* result = PyRun_File(fs,  
		f,  
		Py_file_input,  
		nullptr, nullptr);  
	fclose(fs);  
	Py_FinalizeEx();  
	return 0;  
}  
```  
  
which is exactly the exec_file of boost python.  
  
The call stack is  
  
```  
	ucrtbase.dll!_invoke_watson()	Unknown  
 	ucrtbase.dll!_invalid_parameter()	Unknown  
 	ucrtbase.dll!_invalid_parameter_noinfo()	Unknown  
 	ucrtbase.dll!_read()	Unknown  
 	ucrtbase.dll!common_refill_and_read_nolock<char>()	Unknown  
 	ucrtbase.dll!fgetc()	Unknown  
	python310.dll!check_bom(int(*)(tok_state *) get_char, void(*)(int, tok_state *) unget_char, int(*)(tok_state *, const char *) tok, tok_state *) Line 267	C  
 	python310.dll!tok_underflow_file(tok_state * tok) Line 944	C  
 	[Inline Frame] python310.dll!tok_nextc(tok_state *) Line 1042	C  
 	python310.dll!tok_get(tok_state * tok, const char * * p_start, const char * * p_end) Line 1366	C  
 	[Inline Frame] python310.dll!PyTokenizer_Get(tok_state * tok, const char * *) Line 2053	C  
 	python310.dll!_PyPegen_fill_token(Parser * p) Line 745	C  
 	[Inline Frame] python310.dll!_PyPegen_expect_token(Parser *) Line 900	C  
 	[Inline Frame] python310.dll!_tmp_15_rule(Parser *) Line 22816	C  
 	[Inline Frame] python310.dll!_PyPegen_lookahead(int) Line 891	C  
 	python310.dll!compound_stmt_rule(Parser * p) Line 2145	C  
 	[Inline Frame] python310.dll!statement_rule(Parser *) Line 1490	C  
 	[Inline Frame] python310.dll!_loop1_11_rule(Parser *) Line 22574	C  
 	[Inline Frame] python310.dll!statements_rule(Parser *) Line 1446	C  
 	python310.dll!file_rule(Parser * p) Line 957	C  
 	python310.dll!_PyPegen_parse(Parser * p) Line 34511	C  
 	python310.dll!_PyPegen_run_parser(Parser * p) Line 1325	C  
 	[Inline Frame] python310.dll!_PyPegen_run_parser_from_file_pointer(_iobuf *) Line 1423	C  
 	python310.dll!_PyParser_ASTFromFile(_iobuf * fp, _object * filename_ob, const char * enc, int mode, const char * ps1, const char * ps2, PyCompilerFlags * flags, int * errcode, _arena * arena) Line 26	C  
 	python310.dll!pyrun_file(_iobuf * fp, _object * filename, int start, _object * globals, _object * locals, int closeit, PyCompilerFlags * flags) Line 1199	C  
 	python310.dll!PyRun_FileExFlags(_iobuf * fp, const char * filename, int start, _object * globals, _object * locals, int closeit, PyCompilerFlags * flags) Line 1230	C  
```  
  
However the following code runs smoothly on the same environment  
  
```  
#ifdef _DEBUG  
#undef _DEBUG  
#endif  
  
#define PY_SSIZE_T_CLEAN  
#include <Python.h>  
  
int main(int argc, char* argv[])  
{  
	Py_Initialize();  
	char* f = const_cast<char*>("E:\\Projects\\TestBoostPython\\embedding.py");  
	PyObject* obj = Py_BuildValue("s", f);  
	FILE* file = _Py_fopen_obj(obj, "r+");  
	if (file != NULL) {  
		PyRun_SimpleFile(file, f);  
	}  
	Py_Finalize();  
}  
```

---

## Comment 1

> Username: dublok  
> Created at: 2022-03-05 12:20:04 UTC  
> Url: https://github.com/boostorg/python/issues/383#issuecomment-1059753718  

I found the same issue with a sanitizer:   
```  
==718230==ERROR: AddressSanitizer: heap-use-after-free on address 0x6070001630c0 at pc 0x55ada18f021b bp 0x7fff543e8960 sp 0x7fff543e8110  
READ of size 38 at 0x6070001630c0 thread T0  
    #0 0x55ada18f021a in __interceptor_strlen.part.0 crtstuff.c  
    #1 0x7fe4a23ed1f8 in PyUnicode_DecodeFSDefault downloads/python-3.10.2-build/Python-3.10.2/Objects/unicodeobject.c:4088:35  
    #2 0x7fe4a2478356 in PyRun_FileExFlags downloads/python-3.10.2-build/Python-3.10.2/Python/pythonrun.c:1223:30  
    #3 0x55ada59f5520 in boost::python::exec_file(char const*, boost::python::api::object, boost::python::api::object) downloads/boost-1.78-build/libs/python/src/exec.cpp:128:22  
    #4 0x55ada2ee111c in python::PythonFile::test_method() src/unittest/src/tests/test_python.cpp:176:36  
    ...  
  
0x6070001630c0 is located 32 bytes inside of 70-byte region [0x6070001630a0,0x6070001630e6)  
freed by thread T0 here:  
    #0 0x55ada194fc99 in free (build_clang/unittest/unittest+0x48c2c99)  
    #1 0x55ada59f4b68 in _Py_DECREF tools/python-3.10.2/include/python3.10/object.h:500:20  
    #2 0x55ada59f54db in boost::python::exec_file(char const*, boost::python::api::object, boost::python::api::object) downloads/boost-1.78-build/libs/python/src/exec.cpp:115:3  
    #3 0x55ada2ee111c in python::PythonFile::test_method() src/unittest/src/tests/test_python.cpp:176:36  
    ...  
  
previously allocated by thread T0 here:  
    #0 0x55ada194ff59 in malloc (build_clang/unittest/unittest+0x48c2f59)  
    #1 0x7fe4a233778f in _PyBytes_FromSize downloads/python-3.10.2-build/Python-3.10.2/Objects/bytesobject.c:126:31  
    #2 0x7fe4a233778f in PyBytes_FromStringAndSize downloads/python-3.10.2-build/Python-3.10.2/Objects/bytesobject.c:159:27  
  
SUMMARY: AddressSanitizer: heap-use-after-free crtstuff.c in __interceptor_strlen.part.0  
...  
==718230==ABORTING  
```  
  
  
Possible fix:  
```  
diff --git a/libs/python/src/exec.cpp b/libs/python/src/exec.cpp  
index dd0c331..57f0a16 100644  
--- a/libs/python/src/exec.cpp  
+++ b/libs/python/src/exec.cpp  
@@ -112,7 +112,6 @@ object BOOST_PYTHON_DECL exec_file(char const *filename, object global, object l  
   f = PyBytes_AsString(fb);  
   FILE *fs = fopen(f, "r");  
   Py_DECREF(fo);  
-  Py_DECREF(fb);  
 #elif PY_VERSION_HEX >= 0x03000000  
   // Let python open the file to avoid potential binary incompatibilities.  
   PyObject *fo = Py_BuildValue("s", f);  
@@ -129,6 +128,11 @@ object BOOST_PYTHON_DECL exec_file(char const *filename, object global, object l  
                 f,  
                 Py_file_input,  
 		global.ptr(), local.ptr());  
+  
+#if PY_VERSION_HEX >= 0x03010000  
+  Py_DECREF(fb);  
+#endif  
+  
   if (!result) throw_error_already_set();  
   return object(detail::new_reference(result));  
 }  
  
```
