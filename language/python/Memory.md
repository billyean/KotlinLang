# Memory API

Python defines a couple of platform-independent functions and macros that uses cross platform. Extension modules uses the functions, to ensure binary compatibility across Python versions.

Python wraps all calls to all PyMem_ and PyObject_ memory functions in special debugging wrappers that add additional debugging info to dynamic memory blocks

Three memory APIs have their mapping platform APIs

* PyMem_Malloc - malloc
* PyMem_Realloc - realloc
* PyMem_Free - free

Other Type-oriented memory interface API were defined for better support for Python code.

* PyMem_New - PyMem_Malloc
* PyMem_Resize - PyMem_Realloc
* PyMem_Del - PyMem_Free

Python also defines a PyMemAllocatorEx struct to define user context and its memory APIs.

