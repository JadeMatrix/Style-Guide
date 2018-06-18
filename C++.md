# C++

[ToC]


## File Organization

* include files in the same project by relative path in quoted include, all external files in bracketed include
    * `#include "same_project_same_folder.hpp"`
    * `#include "../same_project/different_folder.hpp"`
    * `#include <external/library.hpp`, configure with build system
* include order:
    * (impl files) any headers this file implements, followed by empty line
    * in alphabetical order first any project files in the same folder, then any project files in other folders, followed by an empty line
    * any third-party includes, followed by an empty newline
    * any C and C++ stdlib includes
* `#line` macro (if used for error messages etc.) should be first in file, never in headers
* headers in `include/`, implementations in `src/`
* namespace grouping
    * group like stuff in their own namespaces, even if that results in multiple `namespace xyz` blocks in the same file
    * allows IDE/editor code folding of stuff not being currently worked on
    * for example, class + non-member util functions implemented in same file; member functions should be in one namespace, other functions in another
* private namespaces for globals & utility functions in impl files
* double newline between major sections:
    * file `#line` macro
    * include guards
    * includes
    * private namespace(s), between each
    * namespace(s), between each
    * include guard `#endif`
* include guard `#endif` should be at end of file except for a single newline


## Naming Conventions

* C++ stdlib style
    * `snake_case` except for template parameters, which use `PascalCase`
    * prefer short names that gain context from their namespace(s) and how they are used
* prefer `using` over `typedef` post C++11
* names should make their use read like a sentence
* iterators returned from `.find()` methods should usually be named `found_…`: `if( found_thing != parent.end() )`
* avoid reiterating type / structure (e.g. `list`, `container`, `get_…`) in name
* names should name exactly what they are, and may be long but try to keep under 4-5 words
    * `generate_and_clear()` or `generate_and_reset()` for the finalize method of a hash generator — don't use `finalize_…` because it's not clear the result is returned
    * `next_sorting_key_between( before, after )`


## Spacing

* single space between tokens
    * increase readability at small font sizes → more code on screen
    * examples:
        * `foo[ i ]`
        * `auto b = 1 + 2.0f;`
        * `func( a, b, c );`
        * `template< typename Foo > void foo( … )`
    * exceptions:
        * within empty scopes: `()`, `[]`, `{}`, `<>`
        * on either side of scope operator `::`
        * on either side of member operator `.`
        * between callable / flow control and parentheses `callable()`/`if( … )` — parens "belong" to function call
        * between container and square brackets `container[]` — brackets "belong" to object/pointer
        * before `;` *except* for multi-line stream format expressions
        * before `template` or template name and `<>` — brackets "belong" to template
        * after unary `-` and `+`
        * before `,`


## Scoping & Indentation

* 4 spaces
* everything indented a multiple of 4 spaces except in a very few cases
* align like segments to emphasize differences
* restrict to 80-char width where practical — only exceptions should be for many long lines of aligned segments
    * ***insert example***
    * may want to use temporary type alias, macros in this case
* always use `()` for lambdas
* first brace for lambdas on same line as `)` — reads best for now, may change as inconsistent with function declarations
* opening curly brace on new line
* scope-close same indent as scope-open
* multi-line stream format expressions have all `<< exp` on a newline 1 indent level, `;` on own line at parent indent


## Formatting Literals

* `N.Nf` for floating-point
* line-splitting of long string literals


## Variable Declaration / Definition / Initialization

* prefer brace-initialization (where possible) post-C++11
* prefer brace-initialization of `auto` post-C++17
* prefer `struct` over `std::pair<>` (situation-dependent)


## Exceptions

* prefer stdlib exceptions unless errors need to be frequently differentiated from those that might arise from stdlib operations


## OOP

* prefer `struct` for POD stuff that doesn't need constraints
* anything that requires semantic constraints should be a class that prohibits invalid state
* constructors:
    * use constructors for unambiguous direct conversions or initialization
    * use static factory methods for ambiguous and/or complex conversions or initialization
    * examples:
        * char-array-to-string would be a constructor
        * string-to-hash would need several factory methods:
            * create hash from incoming string
            * incoming string is already a hash digest as a raw byte string
            * incoming string is already a digest encoded as e.g. hex


## Comments

* todo


## Examples

These are of course contrived to show off formatting edge-cases.

```cpp
std::string make_string( const char* c_string, std::size_t length )
{
    return { c_string, length };
}
```

```cpp
void long_signature(
    const std::string               & arg1,
    const std::vector< std::string >& arg2,
    const std::vector< int         >& arg4,
    const std::array< bool, 3 >     & arg4,
    int                               arg3,
    bool                              arg4
)
{
    // ...
}
```

```cpp
auto formatted = format(
    (
        "error in some_function(): expected machine-readable string token, got "
        "'{}' ({}) instead"
    ),
    foo,
    bar
);
```

```cpp
auto callable = [](){
    std::cout << "hello world" << std::endl;
    return 0;
};
```

```cpp
std::cout
    << "original:\t"
    << original_strings[ n ]
    << std::endl
    << "modified:\t"
    << modified_strings[ n ]
    << std::endl
;
```