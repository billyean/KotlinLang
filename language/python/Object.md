Python defines its own object system that python can garbage collect them when it necessary.

* Python object is using 'reference count' that is increased or decreased when a pointer to the object is copied or deleted. (This is similar to Object C's Automatic Reference Counting model). It's not possible to detect circular reference.(Java uses different approach).
* Python objects are never allocated statically or on the stack; they must be accessed through special macros and functions only.
* Every object has a 'type' that determines what it represents and what kind of data it contains. An object's type is fixed when it is created.

## File
[object.h](https://github.com/python/cpython/blob/master/Include/object.h)

## PyObject_HEAD

PyObject_HEAD defines the initial segment of every PyObject.

## PyObject and PyVarObject

Objects do not float around in memory; once allocated an object keeps the same size and address.  Objects that must hold variable-size data can contain pointers to variable-size parts of the object.  Not all objects of the same type have the same size; but the size cannot change after allocation. 
```c
typedef struct _object {
    _PyObject_HEAD_EXTRA
    Py_ssize_t ob_refcnt;
    struct _typeobject *ob_type;
} PyObject;

typedef struct {
    PyObject ob_base;
    Py_ssize_t ob_size; /* Number of items in variable part */
} PyVarObject;
```

## functions

* _Py_NewReference - Add 1 to object reference count
* PyObject_Init