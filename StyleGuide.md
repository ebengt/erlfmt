# Style Guide

> A programmer does not primarily write code; rather, he primarily writes to
> another programmer about his problem solution. The understanding of this
> fact is the final step in his maturation as technician.
>
> — [What a Programmer Does, 1967][1]

This style guide consists of two sections:
  * Linting - with guidelines about code content that are considered good
    practice and need to be applied manually
  * Formatting - with guidelines about code layout that can be applied
    automatically by using `erlfmt`

## Linting

### Naming
* Use `CamelCase` for variables (acronyms are fine both ways)
```erl
%% Bad
File_name
File_Name
_file
Stored_id
```
```erl
%% Good
FileName
_File
StoredID
StoredId
```

* Use `snake_case` for functions, attributes, and atoms
```erl
%% Bad
'Error'
badReturn
read_File
```
```erl
%% Good
error
bad_return
read_file
```

* Use `SCREAMING_SNAKE_CASE` for macro definitions
```erl
%% Bad
-define(some_value, 100).
-define(someMacro(), hidden:call()).
-define(AnotherMacro, "another macro").
```
```erl
%% Good
-define(SOME_VALUE, 100).
-define(SOME_MACRO(), hidden:call()).
-define(ANOTHER_MACRO, "another macro").
```

* Omit `()` in macro names only if they represent constants
```erl
%% Bad
-define(NOT_CONSTANT, application:get_env(myapp, key)).
-define(CONSTANT(), 100).
```
```erl
%% Good
-define(NOT_CONSTANT(), application:get_env(myapp, key)).
-define(CONSTANT, 100).
```

* Start names of predicate functions (functions that return a boolean)
  with `is_` or `has_` prefix
```erl
%% Bad
evenp(Num) -> ...
```
```erl
%% Good
is_even(Num) -> ...
```

* Avoid using one-letter variable names and `do_` prefixes for functions,
  prefer descriptive names
```erl
%% Bad
validate([X | Xs]) ->
    do_validate(X),
    validate(Xs).
```
```erl
%% Good
validate_all([Key | Keys]) ->
    validate(Key),
    validate_all(Keys).
```

* Use `snake_case` for naming applications and modules
```
%% Good
myapp/src/task_server.erl
```

### Booleans

* avoid `and` and `or` operators, prefer` andalso` and `orelse`, or
  in guards, where possible, combine expressions with `,` and `;`
```erl
%% Bad
is_atom(Name) and Name =/= undefined.
is_binary(Task) or is_atom(Task).
```

```erl
%% Good
is_atom(Name) andalso Name =/= undefined.
is_binary(Task) orelse is_atom(Task).
```

```erl
%% Also good
when is_atom(Name), Name =/= undefined.
when is_binary(Task); is_atom(Task).
```

## Formatting

### Whitespace

* avoid trailing spaces

* end each file with a newline

* use single space after comma
```erl unformatted comma-space
%% Bad
application:get_env(kernel,start_pg2)
```
```erl formatted comma-space
%% Good
application:get_env(kernel, start_pg2)
```

* use single space before and after binary operator (for example:  `=` , `-`, `|`, `||`, `!`, `->`, or `=>`)
```erl unformatted binary-space
%% Bad
Values=[
    #{key=>value},
    #rec{key=value,key2=value2},
    120-90,
    Server!Message
]
```
```erl formatted binary-space
%% Good
Values = [
    #{key => value},
    #rec{key = value, key2 = value2},
    120 - 90,
    Server ! Message
]
```

* do not use space after symbolic unary operators (for example: `+`, `-`)
```erl unformatted unary-space
%% Bad
Angle = - 45.
WithParens = - ( + 45 ).
```
```erl formatted unary-space
%% Good
Angle = -45.
WithParens = -(+45).
```

* do not put spaces before or after `( )`, `{ }`, or `[]`  for function/record/maps/lists (declarations, definitions, calls)
```erl
%% Good
function_definition(Argument) ->
    mod:function_call(Argument).
```

* do not put spaces around segment definitions in binary patterns
```erl unformatted binary-pattern
%% Bad
<<102 : 8 / unsigned-big-integer, Rest / binary>>.
<<255/unsigned - big - integer, Rest/binary>>.
```
```erl formatted binary-pattern
%% Good
<<102:8/unsigned-big-integer, Rest/binary>>.
<<255/unsigned-big-integer, Rest/binary>>.
```

* avoid aligning expression groups
```erl unformatted groups
%% Bad
Module = Env#env.module.
Arity  = length(Args).

inspect(false) -> "false";
inspect(true)  -> "true";
inspect(ok)    -> "ok".
```
```erl formatted groups
%% Good
Module = Env#env.module.
Arity = length(Args).

inspect(false) -> "false";
inspect(true) -> "true";
inspect(ok) -> "ok".
```

# Indentation

* use 4 spaces to indent, no hard tabs
* inside of `case`, `if`, `try`, `receive`, and functions write all clauses on the same line or all clauses on separate lines, don't mix two clause styles.

```
%% Bad
lookup(1) -> one;
lookup(2) ->
    two.

%% Good
lookup(1) -> one;
lookup(2) -> two.

%% Also good
lookup(1) ->
    one;
lookup(2) ->
    two.
```

* always break comma-separated expressions across multiple lines:
```
%% Bad
run() -> compile(), execute().

%% Good
run() ->
    compile(),
    execute().

%% Bad
case application:get_env(kernel, start_pg2) of
    {ok, true} -> pg2:start();
    undefined -> ?LOG_NOTICE("skipping pg2 start"), ok
end

%% Good
case application:get_env(kernel, start_pg2) of
    {ok, true} ->
        pg2:start();
    undefined ->
        ?LOG_NOTICE("skipping pg2 start"),
        ok
end

%% Bad
case Processed = process(Val), info(Processed) of
    {info, Some} -> ok;
    {error, Reason} -> handle_error(Reason)
end

%% Good
case
    Processed = process(Val),
    info(Processed)
of
    {info, Some} -> ok;
    {error, Reason} -> handle_error(Reason)
end

%% Also good (and has the same semantics)
Processed = process(Val),
case info(Processed) of
    {info, Some} -> ok;
    {error, Reason} -> handle_error(Reason)
end
```
* Indent the right-hand side of a binary operator if it is on a different line
```erlang
%% Bad
f(Op, Atom) ->
    is_atom(Op) andalso
    Atom =/= '!' andalso
    Atom =/= '='.

%% Good
f(Op, Atom) ->
    is_atom(Op) andalso
        Atom =/= '!' andalso
        Atom =/= '='.
```
* When assigning the result of a multi-line expression, begin the expression on a new line
```erlang
%% Bad
Prefix = case Base of
             binary -> "2#";
             octal -> "8#";
             hex -> "16#"
         end.

%% Good
Prefix =
    case Base of
        binary -> "2#";
        octal -> "8#";
        hex -> "16#"
    end.
```

# Exports

Every exported function occupies exactly 1 line, comma following the definition:

```erlang
%% Good
-export([
    api_function/1,
    api_another/2,
    api_third/3
]).
```

It is acceptable to have the same function (with different arity) on the same line:

```
%% Also good
-export([
    api_one/1, api_one/2, api_one/3
]).
```

# Strings

* do not use multilne strings
```erlang
%% Bad
Message = "erlfmt is a code formatter for Erlang.

Arguments:

  files -- files to format
  ..."

%% Good
Message =
    "erlfmt is a code formatter for Erlang.\n\n"
    "Arguments:\n\n"
    "  files -- files to format\n"
    "..."

%% Also good
Message =
    "erlfmt is a code formatter for Erlang.\n"
    "\n"
    "Arguments:\n"
    "\n"
    "  files -- files to format\n"
    "..."
```

# Line length

Line length must not exceed **120** characters.

[1]: http://archive.computerhistory.org/resources/text/Knuth_Don_X4100/PDF_index/k-9-pdf/k-9-u2769-1-Baker-What-Programmer-Does.pdf
