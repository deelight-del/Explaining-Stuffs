# Trying to demystify the advanced task of List task

[Julien Youtube](https://www.youtube.com/watch?v=4JihuTAOaMI) will likely help a lot especially with vizualizations.

The task given is to be able to print some information about a given list datatype (which is acually a `python` datatype). In the given assignment, we are to write a C function that will then be used in a python file through some dynamic linking and compilation.
> P:S - You don't need to know a lot about dynamic linking to be able to complete this task. And going forward, I will be focusing on what you need to know.

Our C function is of the prototype, 

```C
    void (PyObject *)
```

## Let us talk about Python and PyObject.
By now, you might have heard the phrase "Everything is an object in Python".
Let us take deeper look about what this concept is really about. 

> **FOR BEGINNERS: A little recap about the Python language and its interpreter:**
	The Python interpreter is a program that **interpretes** a 
	Python code (written in Python language).
	A Python interpreter's behaviour can easily be compared 
	with how a shell behaves.
	When you enter some certain `command` on a simple shell, such `command` 
	is executed using the machine's
	operating system (OS) kernel (on an oversimplified level, this could be said
	as executing the command on the
	computer's processor directly). 
	The `command` is usually a pathname to a binary executable on your machine. 
	The result of such execution is thus displayed to your output.
	In the same vein, with the python interpreter, 
	when you enter a command (a script, some function,
	method, or instantiation of classes), the given command is taken to a "backroom". 
	This backroom is equivalent to how the simple shell takes a given command to the
	backroom and execute the respective executable with the OS kernel.
	In the Python's backroom actually, most (if not all) of its 
	executable commands are not ready made executables.
	Instead, a typical Python's backroom consist of a lot of interfaces 
	that allows the execution of your command 
	on the python interpreter. The interface typically includes files from 
	the lower level language that the 
	python interpreter is written in.
	IN OUR CASE HERE, We are dealing with CPython, 
	which tells us that our interpreter is
	a binary executable that was executed in the 
	C programming language, and our interface is usually a bunch of header 
	files that the python interpreter needs to work.
	In a typical LINUX  machine, you should find your interface files in this directory: 
	`usr/include/pythonX.Y` [Wher X.Y is the version number of the python in use, e.g 3.10].



Every Python Object can be represented as a **pointer to a PyObject** 
under the hood (i.e in the C programming language, which is used to compile the Python interpreter). 
This PyObject is a user defined datatype in reference to C. 
The struct definition of PyObject is given below (as seen in the `object.h` file):
		


```C 
typedef struct _object 
	{
		_PyObject_HEAD_EXTRA; 
		Py_ssize_t ob_refcnt;
		PyTypeObject *ob_type;
	} PyObject;
```


- **The first element:** `_Py_ObjectHEAD_EXTRA` is a macros that an 
object type uses for inheritances. (Open to contribution and discussion)

- **The second element:** `ob_refcnt` is of datatype `Py_ssize_t` (an alias of `ssize_t`).
`ob_refcnt` is the reference count which is used in memory management (Garbage Collection).

- **The third element:** `*ob_type` is the datatype of a respective given obect. 
It is a pointer to another `struct` datatype called `PyTypeObject` that also contain some fields 
as found in the [link](https://docs.python.org/3/c-api/typeobj.html).

There is also a variation of the PyObject datatype called `PyVarObject`, 
that has an extra field called the `size` field. 
In a simple nutshell, every object datatype is constructed 
with `PyObject struct` as a base. 
Every Object datatype constructed from PyObject 
can (be converted to) behave as a PyObject.
Note, that the PyObject struct is never actually used directly 
to construct a Python Object. Instead, a PyObject pointer is used.

PyObject or/and PyVarObject are thus used as the most basic 
constructors to construct
Python Objects including integers, floats, lists, tuples, e.t.c.

In our case and consideration, we are looking at the Python Object list, 
which is the `PyListObject`, which is defined with the following fields below:


```C 
typedef struct {
	PyObject_VAR_HEAD;
	PyObject **ob_item;
	Py_ssize_t allocated;
} PyListObject;
```

The most important field that I will highlight is the `ob_item` field which is a pointer to a series of PyObject pointers. 
And this totally makes sense, as we are expecting our list to contain a series of Python Objects. 
The `allocated` field is an integer that represents the number of allocated slots for the list 

Before, I round up, and open this up for collaboration, I will discuss something quickly.

From the discussions so far, you can see that all python objects are just a bunch of struct defined datatypes 
that can contain nested struct data type. And if you have ever worked with structs and pointers,
you will agree that it can get messy working with pointers and deferencing. For this kind of reason, 
it is important to note that there are already defined macros and functionst that can help you retrieve certain kind of results, 
objects, elements, or fields from some given object.

[Functions and Macros for PyObjects](https://docs.python.org/3.4/c-api/structures.html)

[Functions and Macros for PyListObjects](https://docs.python.org/3.4/c-api/list.html)

Written: By Akingbeni David


