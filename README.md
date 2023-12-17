# MSVC, Building a library

### Using libraries and why it matters.

**Wikipedia** : "The value of a library lies in the reuse of standardized program elements. When a program invokes a library, it gains the behavior implemented inside that library without having to implement that behavior itself. Libraries encourage the sharing of code in a modular fashion and ease the distribution of the code."

#### Project
Example details are grouped into subject related points and showcase investigation result on the subject of libraries. Further details will be provided on more advance features that are covered in the making of our examples.

#### What this README.md is not
Be aware that some of the tooling used in the making of this `Demo` project won't be covered here. External documentations will be provided for your own benefit, which in most case, are also where most of the information mentioned here will be coming from.

### What's a library

**Wikipedia** : "A library is a collection of implementations of behavior, written in terms of a language, that has a well-defined interface by which the behavior is invoked. For instance, people who want to write a higher-level program can use a library to make system calls instead of implementing those system calls."

## Types of libraries

#### Header-only Library

A library is called header-only if the full definitions of all macros, functions and classes comprising the library are visible to the compiler in a header file form. Header-only libraries do not need to be separately compiled, packaged and installed in order to be used.

##### Example - MSVC Development Toolkit Cmd-line

```
C:> cl Main.cc		(Main.obj, Main.exe)
```

```
// header-only library

#include <type_traits>

class FMath {
public:
	template<typename T,
		std::enable_if_t<std::is_arithmetic<T>::value, bool> = true>
	inline static T Sum(const T& A, const T& B) {
		return A + B;
	}
};

// executable

#include <stdio.h>
#include "Math.hh"

int main(int argc /*arg count*/, char* argv[] /*arg values*/) {

	printf("%i",  FMath::Sum<int  >(1    , 2    ));
	return 0;
};
```

#### Static library

A static library or statically-linked library is a set of routines, external functions and variables which are resolved in a caller at compile-time and copied into a target application by a compiler, linker, or binder, producing an object file and a stand-alone executable.

##### Example
```
C:> cl /c HelloWorld.cc			(HelloWorld.obj)
C:> lib HelloWorld.obj			(HelloWorld.lib)
C:> cl Main.cc /link HelloWorld.lib	(Main.obj, Main.exe)
```

```
// static library

extern "C" const char* __cdecl Print() {
	return "Hello World!";
}

// executable

#include <stdio.h>

// forward declaration
extern "C" const char* __cdecl Print();

int main(int argc /*arg count*/, char* argv[] /*arg values*/) {

	puts(Print());
	return 0;
};
```

#### Shared library

A shared library or shared object is a file that is intended to be shared by executable files and further shared object files. Modules used by a program are loaded from individual shared objects into memory at load time or runtime, rather than being copied by a linker when it creates a single monolithic executable file for the program.

##### Example
```
C:> cl /c GetGreetings.cc			(GetGreetings.obj)
C:> lib GetGreetings.obj			(GetGreetings.lib)
C:> link GetGreetings.obj /DLL /NOENTRY		(GetGreetings.dll)
C:> cl Main.cc /link GetGreetings.lib		(Main.obj, Main.exe)
```

**Wikipedia** : 

* Static
* Dynamic
* Advantages of one over the other
* Implication at the executable level
* How to build one
* the Implications with the Linker
* HOw your OS query dynamic libraries

#### A Simple HelloWorld.cc
```
// some example code
```

##### SYMBOLS
**__cdecl** 

##### DUMPBIN

* https://learn.microsoft.com/en-us/cpp/cpp/extern-cpp?view=msvc-170
* https://learn.microsoft.com/en-us/cpp/cpp/cdecl?view=msvc-170
* https://learn.microsoft.com/en-us/windows/win32/dlls/dynamic-link-libraries
* https://learn.microsoft.com/en-us/cpp/build/dlls-in-visual-cpp?view=msvc-170
* dll with entry point vs no entry point
