# gcc_findings
Exploring how to use gcc to manually build external libraries. Also a bit of zsh exploration.

## Part 1: Reference

| **key** | **description** |
| --- | --- |
| `<something>` | replace with something |

| **extensions** | **description** |
| --- | --- |
| `.cpp` | c++ source |
| `.c` | c source |
| `.hpp` | c++ header |
| `.h` | c header |
| `.i` | pre-processed code |
| `.s` | assembly code |
| `.dot` | a diagram of the syntax tree |
| `.o` | object file |
| `.dylib` | Macs dynamic library format |
| `.dll` | Microsofts dynamic library format |
| `.so` | Linuxs dynamic library format |
| `.exe` | executable |

| **flags** | **description** |
| --- | --- |
| `-E` | reveals the `.i` file(s) or pre-processed file(s). |
| `-S <.cpp file(s)>` | reveals the `.s` file(s) or assembly file(s) |
| `-c <.cpp-file(s)>` | converts the `.cpp` file(s) into `.o` file(s). |
| `-t` | changes the details seen from the `objdump` program. |
| `-I/<path>` | specifies a non stardard path to search for includes. |
| `-l<sfml-window>` | implicit library to be found in the stardard path |
| `-L/<path>` | explicit path to library. |
| `-o <app> <.o file(s)>` | links the `.o` file(s) into an executable. |
| `-fdump-tree-all-graph` | reveals the `.dot` file(s) for syntax trees. |
| `-g` | reveals debugging information. |
| `-Wall` | reveals most warnings. |
| `-O2` | turns on optimization. |
| `-E` | reveals what the pre-processor is doing. |

| **tools** | **description** |
| --- | --- |
| `g++` |  invokes the gcc tools. |
| `xdot <.dot file(s)>` | invokes the syntax tree viewing. |
| `objdump <.o file(s)>` | shows what the object file(s) look like. |
| `ldd <.exe>` | reveals the standard paths being searched for libraries |

## Part 2: the Stages

![](/diagram.png)

Currently this is the most common pattern I think is important
```zsh
g++ -c <location-of-.cpp-files> -I/<search-path-for-includes>
g++ -o <build-name> <object-files-to-link>
mv <stuff> <place>
```

# Main
```
-c <.cpp file(s)>		// compile .cpp file(s) into .o file(s)
-I/<add-this-search-plath>	// Specify search path for angle bar includes, must be specific
-L <library-path>		// still don't understand
-o <build-name> <.o file(s)>	// builds an executable
```

# Misc
```
-g 	// turns on debugging
-Wall 	// turns on most warnings
-O2 	// turns on optimizations
```

# Part 1: "Flat" Directories

#### dir

```
/project
	main.cpp
```

#### main.cpp

```cpp
#include <iostream>

int main() {
	std::cout << "Hello, World!\n";
	return 0;
}
```

#### steps:

```zsh
g++ -c main.cpp
g++ -o app main.o
./app

Hello, World!
```

---

two file "flat" setup. both are cpp files.

#### dir

```
/project
	foo.cpp
	main.cpp
```

#### foo.cpp

```cpp
#include <iostream>

struct Foo {
	void f() {
		std::cout << "Hello, World!\n";
	}
};
```

#### main.cpp

```cpp
#include "foo.cpp"

int main() {
	Foo x;
	x.f();
	return 0;
}
```

#### steps:

```zsh
g++ -c foo.cpp
g++ -c main.cpp
g++ -o app main.o
./app

Hello, World!
```

---

three file "flat" setup. header file with quotes.

#### dir

```
/project
	foo.h
	foo.cpp
	main.cpp
```

#### foo.h

```h
#pragma once

struct Foo {
	void f();
};
```

#### foo.cpp

```cpp
#include "foo.h"
#include <iostream>

void Foo::f() {
	std::cout << "Hello, World!\n";
}
```

#### main.cpp

```cpp
#include "foo.h"

int main() {
	Foo x;
	x.f();
	return 0;
}
```

#### steps:

```zsh
g++ -c foo.cpp
g++ -c main.cpp
g++ -o app foo.o main.o
./app

Hello, World!
```

---

three file "flat" setup. header file with angle bars.

#### dir

```
/project
	foo.h
	foo.cpp
	main.cpp
```

#### foo.h

```h
#pragma once

struct Foo {
	void f();
};
```

#### foo.cpp

```cpp
#include <foo.h>
#include <iostream>

void Foo::f() {
	std::cout << "Hello, World!\n";
}
```

#### main.cpp

```cpp
#include <foo.h>

int main() {
	Foo x;
	x.f();
	return 0;
}
```

#### steps:

```zsh
g++ -c foo.cpp -I/$(pwd)/
g++ -c main.cpp -I/$(pwd)/
g++ -o app foo.o main.o
./app

Hello, World!
```

---
# Part 2: "Nested" Directories

header "nested" in an include folder. header file has angled brackets.

#### dir

```
/project
	/include
		foo.h
	foo.cpp
	main.cpp
```

#### foo.h

```h
#pragma once

struct Foo {
	void f();
};
```

#### foo.cpp

```cpp
#include <foo.h>
#include <iostream>

void Foo::f() {
	std::cout << "Hello, World!\n";
}
```

#### main.cpp

```cpp
#include <foo.h>

int main() {
	Foo x;
	x.f();
	return 0;
}
```

#### steps:

```zsh
g++ -c foo.cpp -I/$(pwd)/include
g++ -c main.cpp -I/$(pwd)/include
g++ -o app foo.o main.o
./app

Hello, World!
```

---

two folder setup, with header and cpp file seperated.

#### dir

```
/project
	/include
		foo.h
	/src
		foo.cpp
	main.cpp
```

#### foo.h

```h
#pragma once

struct Foo {
	void f();
};
```

#### foo.cpp

```cpp
#include <foo.h>
#include <iostream>

void Foo::f() {
	std::cout << "Hello, World!\n";
}
```

#### main.cpp

```cpp
#include <foo.h>

int main() {
	Foo x;
	x.f();
	return 0;
}
```

#### steps:

```zsh
g++ -c src/foo.cpp -I/$(pwd)/include
g++ -c main.cpp -I/$(pwd)/include
g++ -o app foo.o main.o
./app

Hello, World!
```

or

```zsh
g++ -c src/*.cpp -I/$(pwd)/include
g++ -c main.cpp -I/$(pwd)/include
g++ -o app foo.o main.o
./app

Hello, World!
```

---

lib folder with seperated header and cpp files.

#### dir

```
/project
	/lib
		/include
			foo.h
		/src
			foo.cpp
	main.cpp
```

#### steps:

```zsh
g++ -c lib/src/foo.cpp -I/$(pwd)/lib/include
g++ -c main.cpp -I/$(pwd)/lib/include
g++ -o app foo.o main.o
./app

Hello, World!
```

or 

```zsh
g++ -c lib/src/*.cpp -I/$(pwd)/lib/include
g++ -c main.cpp -I/$(pwd)/lib/include
g++ -o app foo.o main.o
./app

Hello, World!
```

---

multiple cpp and h files nested

#### dir

```
/project
	/include
		foo.h
		bar.h
	/src
		foo.cpp
		bar.cpp
	main.cpp
```

#### foo.h

```h
#pragma once

struct Foo {
	void f();
};
```

#### foo.cpp

```cpp
#include <foo.h>
#include <iostream>

void Foo::f() {
	std::cout << "Hello, Foo!\n";
}
```

#### bar.h

```h
#pragma once

struct Bar {
	void b();
};
```

#### bar.cpp

```cpp
#include <bar.h>
#include <iostream>

void Bar::b() {
	std::cout << "Hello, Bar!\n";
}
```


#### main.cpp

```cpp
#include <foo.h>
#include <bar.h>

int main() {
	Foo x;
	x.f();

	Bar y;
	y.b();
	return 0;
}
```

#### steps:

```zsh
g++ -c lib/src/*.cpp -I/$(pwd)/lib/include
g++ -c main.cpp -I/$(pwd)/lib/include
g++ -o app *.o
./app

Hello, Foo!
Hello, Bar!
```

---

move object files into a bin folder

#### dir structure
```
/project
	/bin
	/lib
		/include
			bar.h
			foo.h
		/src
			bar.cpp
			foo.cpp
	main.cpp
```

#### steps:

```zsh
g++ -c lib/src/*.cpp -I/$(pwd)/lib/include
g++ -c main.cpp -I/$(pwd)/lib/include
g++ -o app *.o
mv *.o bin
mv app bin
./bin/app

Hello, Foo!
Hello, Bar!
```

---
