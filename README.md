# MSVC, Building a library

### Using libraries and why it matters.

**Wikipedia** : "The value of a library lies in the reuse of standardized program elements. When a program invokes a library, it gains the behavior implemented inside that library without having to implement that behavior itself. Libraries encourage the sharing of code in a modular fashion and ease the distribution of the code."

#### Project
Example details are grouped into subject related points and showcase investigation result on the subject of libraries. Further details will be provided on more advance features that are covered in the making of our examples.

#### What this README.md is not
Be aware that some of the tooling used in the making of this `Demo` project won't be covered here. External documentations will be provided for your own benefit, which in most case, are also where most of the information mentioned here will be coming from.

## Quick guide to CLI (Command-line Interface)

* [cl](https://learn.microsoft.com/en-us/cpp/build/reference/compiler-options?view=msvc-170) : The compilers produce Common Object File Format (COFF) object (.obj) files. The linker produces executable (.exe) files or dynamic-link libraries (DLLs). To compile without linking, use the [/c](https://learn.microsoft.com/en-us/cpp/build/reference/cl-invokes-the-linker?view=msvc-170) option.
* [lib](https://learn.microsoft.com/en-us/cpp/build/reference/lib-reference?view=msvc-170) : The Microsoft Library Manager (LIB.exe) creates and manages a library of Common Object File Format (COFF) object files. LIB can also be used to create export files and import libraries to reference exported definitions.
* [link](https://learn.microsoft.com/en-us/cpp/build/reference/linker-options?view=msvc-170) : LINK.exe links Common Object File Format (COFF) object files and libraries to create an executable (EXE) file or a dynamic-link library (DLL).
* [dumpbin](https://learn.microsoft.com/en-us/cpp/build/reference/dumpbin-reference?view=msvc-170) : The Microsoft COFF Binary File Dumper (DUMPBIN.EXE) displays information about Common Object File Format (COFF) binary files. You can use DUMPBIN to examine COFF object files, standard libraries of COFF objects, executable files, and dynamic-link libraries (DLLs).


## What's a library

**Wikipedia** : "A library is a collection of implementations of behavior, written in terms of a language, that has a well-defined interface by which the behavior is invoked. For instance, people who want to write a higher-level program can use a library to make system calls instead of implementing those system calls."

# Types of libraries

### Header-only library

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

### Static library

A static library or statically-linked library is a set of routines, external functions and variables which are resolved in a caller at compile-time and copied into a target application by a compiler, linker, or binder, producing an object file and a stand-alone executable.

##### Example - MSVC Development Toolkit Cmd-line

```
C:> cl /c HelloWorld.cc				(HelloWorld.obj)
C:> lib HelloWorld.obj				(HelloWorld.lib)
C:> cl Main.cc /link HelloWorld.lib		(Main.obj, Main.exe)
```
*Tips : Now that the executable has been compiled and linked with the static library (.lib), the executable can invoke functions defined in the static library without the .lib existing as its now compiled into the executable. You can safely delete the .obj file and .lib file and run the executable.*

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

### Shared library

A shared library or shared object is a file that is intended to be shared by executable files and further shared object files. Modules used by a program are loaded from individual shared objects into memory at load time or runtime, rather than being copied by a linker when it creates a single monolithic executable file for the program.

**Load-time dynamic linking**

In load-time dynamic linking, an application makes explicit calls to exported DLL functions like local functions. To use load-time dynamic linking, provide a header (.h) file and an import library (.lib) file when you compile and link the application. When you do this, the linker will provide the system with the information that is required to load the DLL and resolve the exported DLL function locations at load time.

##### Example A - MSVC Development Toolkit Cmd-line

```
C:> cl /c GetGreetings.cc			(GetGreetings.obj)
C:> link GetGreetings.obj /DLL /NOENTRY		(GetGreetings.dll)
C:> cl Main.cc /link GetGreetings.lib		(Main.obj, Main.exe)
```
*Tips : Now that the executable has been compiled and linked with the import library (.lib), the executable will access the dll exported functions when invoked. You can safely delete the .obj file and .lib file and run the executable.* 

```
// shared library

__declspec(dllexport) const char* __cdecl GetGreetings() {
	return "Greatings Programmer!";
}

// executable

#include <stdio.h>

__declspec(dllimport) const char* __cdecl GetGreetings();

int main(int argc /*arg count*/, char* argv[] /*arg values*/) {

	puts(GetGreetings());
	return 0;
};
```

**Run-time dynamic linking**

In run-time dynamic linking, an application calls either the LoadLibrary function or the LoadLibraryEx function to load the DLL at run time. After the DLL is successfully loaded, you use the GetProcAddress function to obtain the address of the exported DLL function that you want to call. When you use run-time dynamic linking, you do not need an import library file.

##### Example B - MSVC Development Toolkit Cmd-line

```
C:> cl /c GetGreetings.cc					(GetGreetings.obj)
C:> link GetGreetings.obj /DLL /NOENTRY	/EXPORT:GetGreetings	(GetGreetings.dll)
C:> cl Main.cc							(Main.obj, Main.exe)
```

```
// shared library

extern "C" const char* __cdecl GetGreetings() {
	return "Greatings Programmer!";
}

// executable

#include <stdio.h>
#include <Windows.h>

int main(int argc /*arg count*/, char* argv[] /*arg values*/) {

	const HMODULE ModuleHandle = LoadLibraryExW(L"GetGreetings.dll", nullptr, 0);
	using GetGreetingType = const char* (__cdecl*)();
	const GetGreetingType GetGreetings = reinterpret_cast<GetGreetingType>(GetProcAddress(ModuleHandle, "GetGreetings"));

	puts(GetGreetings());
	FreeLibrary(ModuleHandle);
	return 0;
};
```
Tips : Further details are provided regarding window's specific usage of dll's at the following [link](https://learn.microsoft.com/en-us/troubleshoot/windows-client/deployment/dynamic-link-library). Additional information about Dll can be found on [cppcon](https://www.youtube.com/watch?v=JPQWQfDhICA&t=579s&ab_channel=CppCon) channel.
