# C++

[ToC]


## General Usage

* always compile with all possible warnings enabled; this will help signal bad design, unsafe operations, and broken code (written that way or after a refactor). for example, something like this (seen in production code, thankfully not security-critical):

```cpp
void seed_random( int seed )
{
    impl.srand();
}
```

* use CMake for build configuration; see [CMake Style Guide](CMake.md)


## File Organization

* headers in `include/<name>/`, implementations in `src/`
* headers in same library/executable include each other using relative (quoted) includes, source files include with lookup (angle brackets, e.g. `#include <my_project/foo.hpp>`)
* include order:
    * (impl files) any headers this file implements, followed by empty line
    * in alphabetical order first any project files in the same folder, then any project files in other folders, followed by an empty line
    * any third-party includes, followed by an empty newline
    * any C and C++ stdlib includes
* `#line` macro (if used for error messages etc.) should be first in file, never in headers
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
* include guard `#endif` should be at end of file, followed by a single newline
* include guards should start with a `#pragma once` for compilers that support it, followed by traditional `#ifdef ...`


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

* use spaces not tabs
* everything indented a multiple of 4 spaces except in a very few cases
* restrict lines to 80 characters (not including newline char) where practical — only exceptions should be for many long lines of aligned segments
    * ***insert example***
    * may want to use temporary type alias, macros in this case
    * prefer range-based-for over `std::initializer_list` (of possibly a single-use `struct` type) to many similar lines (insert Carmack citation)
* align like segments to emphasize differences
* always use `()` for lambdas
* first brace for lambdas on same line as `)` — reads best for now, may change as inconsistent with function declarations
* opening curly brace on new line
* scope-close same indent as scope-open
* argument lists that would take the line past 80 characters should be split one argument per line, indented one stop, and followed by the comma; the closing parentheses should be indented back to the original level
    * this rule is recursive for function calls used as arguments
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
        * for single-argument constructors these may or may not be marked `explicit` depending on how safe/performant an implicit conversion would be
    * use static factory methods ("named constructors") for ambiguous and/or complex conversions or initialization
    * examples:
        * char-array-to-string would be a constructor
        * string-to-hash would need several factory methods:
            * create hash from incoming string
            * incoming string is already a hash digest as a raw byte string
            * incoming string is already a digest encoded as e.g. hex
* when to use classes
    * classes should be used as resource managers
    * any functions that do not need direct access to an object's internal state should be free functions ([How Non-Member Functions Improve Encapsulation by Scott Meyers](http://www.drdobbs.com/cpp/how-non-member-functions-improve-encapsu/184401197))
        * this includes any static member functions that may need to access the internal state of some object of that type, such as named constructors above
    * if an object has no internal state it should have no member functions, and probably shouldn't exist
        * caveat for traits- and tag-types
        * caveat for template metaprogramming, especially pre-C++14


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

Some real examples, sort out later:

```cpp
show::response response{
    request.connection(),
    show::http_protocol::HTTP_1_0,
    { 200, "OK" },
    {
        server_header,
        { "Content-Type"  , { "text/html"                   } },
        { "Content-Length", { std::to_string( form.size() ) } }
    }
};

analysis
    << " -  segment with size "
    << content.size()
    << " bytes, headers:\n"
;

std::string message{
    "HTTP/"
    + std::string{ is_1p1 ? "1.1" : "1.0" }
    + " "
    + request.method()
    + " request from "
    + request.client_address()
    + " on path /"
};

if( upload_data.data && (
       needs_alpha_pass
    || needs_drop_pass
    || needs_gamma_pass
) )
{
    // ...
}

template<
    class AttributeBuffer,
    class Framebuffer,
    class IndexableNames
>
void bind(
    gl::shader_program< AttributeBuffer, Framebuffer >& program,
    const IndexableNames& names
) const
{
    // ...
}
```
