Elixir cheatsheet: https://devhints.io/elixir#enum

- Elixir built-in executables:
	- elixir
	- elixirc
	- iex - run elixir interactive shell
	- mix ([docs](https://hexdocs.pm/mix/Mix.html)) - built-in all-in-one tool for:
		- creating (`mix new`), compiling (`mix compile`), testing (`mix test`) elixir files
		- managing dependencies
		- code formatting (`mix format`)
- Elixir built-in libraries etc:
	- ExUnit - unit-testing framework
	- OTP (Open Telecom Platform) - A set of powerful libraries and tools inherited from Erlang

The usual way to refer to specific function is `<function_name>/<argument_count>`. For example `-/2` refers to `Kernel.-` function that takes `2` arguments (has an arity of 2).

`defp` - Keyword for defining a named private function inside of a module. A private function cannot be used outside of that module.

Once the project is compiled (`mix compile`), you can start an iex session inside the project by running the command below. The -S mix is necessary to load the project in the interactive shell:
```
$ iex -S mix
```
To recompile during an iex session use:
```
recompile()
```

The difference between `IO.puts()` and `IO.inspect()` is that the latter additionaly returns the value "inspected", so that you can use it in between pipe operators:
```
"hello"
|> String.upcase()
|> IO.inspect()
|> String.split("", trim: true)
```

## Tests
Mix projects usually follow the convention of having a `<filename>_test.exs` file in the `test` directory for each `.ex` file in the `lib` directory. Notice how tests use elixir script file extension! It's because they don't need to be compiled before running.
To run only one specific test on line 5 use:
```
$ mix test test/kv_test.exs:5
```

## Environments
Environments allow a developer to customize compilation and other options for specific scenarios. Default environments are:
- `:dev` - the one which Mix tasks (like `compile`) run by default
- `:test` - used by `mix test`
- `:prod` - the one you will use to run your projects in production
Customization can by accessing the `Mix.env` function in `mix.exs` file.


## Help with mix
You can list all available tasks by using:
```
$ mix help
```
You can get further information about a particular task by invoking:
```
$ mix help <task>
```

## Basic syntax
- `string1 <> string2` - string concatenation
- `0xF === 15` returns `true`
- `0b1010 === 10` returns `true`
- `0o777 === 511` returns `true`
- Float numbers support `e` for scientific notation: `1.0e-10`
- `1.0 == 1` returns `true`
- `1.0 === 1` returns `false`
- `:abc` - atom/symbol
- `"example"` - string
- `[1, 2, 3]` - list
-  `{1, 2, 3}` - tuple
- Check if a value is a boolean or not: `is_boolean(x)`. Similar functions: `is_integer/1`, `is_float\1` and `is_number/1`.
- Elixir has a construct called aliases. Aliases start in upper cases and are also atoms: `is_atom(Hello)` returns `true`.
- Use `nil` to represent a value unknown/missing. `nil` is equal to `:nil`.

> Implementation detail: The booleans `true` and `false` are actually `:true` and `:false`. So `false === :false` returns `true`. Also `is_atom(false)` and `is_boolean(:false)` both return `true`.

## Basic math
- `div(10, 2)` returns `5`
- `rem(5, 2)` returns `1`
- `round(3.58)` returns `4`
- `trunc(3.58)` returns `3`

> Friendly tip: Parantheses in elixir are optional, but you should use them to stick to the convention.

## Docs
- Use `h trunc/1` in `iex` for docmentation on that function. It works because `trunc/1` is defined in the `Kernel` module. All functions in the `Kernel` module are automatically imported into our namespace.
- You can use module+function to lookup for anything: `h Kernel.+/2`

## Strings
- String interpolation (like f-strings in Python): `"hello #{variable1}"`
- Get the number of bytes in a string: `byte_size("hellö")`. That won't always be equivalent to the length of the string though.
- Get length of a string: `Get.length("hellö")`
- `String.upcase("example")` returns `"EXAMPLE"`
- Single quotes are charlists (not strings), double quotes are strings.

## Functions
- Define an anonymous functions: `add = fn a, b -> a + b end`
- To invoke an anonymous function a dot is required: `add.(2, 2)`. The dot is required to distinguish between a function assigned to a variable `add` and a named function `add/2`.
- A variable assigned inside a function does not affect its surroundnig environment at all:
```
iex> x = 42
42
iex> (fn -> x = 0 end).()
0
iex> x
42
```

## Lists
- Values can be of any type.
- `length [1, 2, 3]` returns `3`
- Two list can be concatenated or substracted using the `++/2` and `--/2` operators:
```
iex> [1, 2] ++ [5, 6]
[1, 2, 5, 6]
iex> [1, true, 2, false, 3, true] -- [true, false]
[1, 2, 3, true]
```
- List operators never modify the existing list. They simply return a new list. We say that Elixir data structures are *immutable*. It leads to clearer code. You can freely pass the data.
- We call the first element of the list "head of the list" and the remainder "tail of the list". They can be retrieved with the functions `hd/1`, `tl/1`.
- The performance of list concatenation depends on the left-hand list. Prepending (`[0] ++ list`) is faster than appending (`list ++ [0]`), but for short lists it doesn't make much difference.
- `[head | tail]` format can be used for pattern matching as well as prepending items to a list:
```
iex> [:new_element | [1, 2, 3]]
[:new_element, 1, 2, 3]
```
- When you want to get only the head of the list and not the tail use underscore to represent a value to be ignored (and that can't be read from).


> When you see a value in IEx and you are not quite sure what it is, you can use the `i/1` to retrieve information about its data type, raw representation and more.

## Tuples
- `tuple_size {:ok, "hello"}` returns `2`
- Tuples are indexed from 0. To acces second element of a tuple `tuple1` use `elem(tuple1, 1)`.
- You can put an element at specified index with `put_elem/3`. 
```
iex> tuple = {:ok, "hello"}
{:ok, "hello"}
iex> put_elem(tuple, 1, "world")
{:ok, "world"}
iex> tuple
{:ok, "hello"}
```
- Like lists, tuples are also immutable.
- The difference between tuples and llists is that lists are stored as linked lists and tuples are stored contiguosly in memory. This means getting the element count or accessing an element by index is faster in tuples. However, updating or adding elements to tuples is expensive as it requires creating a new tuple in memory.
- One very common use case for tuples is to return extra information from a function. For example, `File.read("path/to/file1"` returns a tuple `{:ok, "contents of file1"}`.

## Comparison
- Use `and`, `or`, and `not` when you are expecting booleans. If any of the arguments are non-boolean, use `&&`, `||`, and `!`.
- The only difference between `==` and `===` is that the latter will return `false` when comparing integers and false.
- It's possible to compare different data types. The type "value", from lower to higher, is: `number, atom, reference, function, port, pid, tuple, map, list, bitstring`.

## The match operator `=`
- Tuple unpacking:
```
iex> {a, b, c} = {:hello, "world", 42}
{:hello, "world", 42}
iex> a
:hello
iex> b
"world"
```
- We can match on specific values. The example below asserts that the left side will only match the right side when right side is a tuple that starts with the atom `:ok`. The new variable `result` will be assigned
```
iex> {:ok, result} = {:ok, 13}
{:ok, 13}
iex> result
13
```
- A list also supports matching on its own head and tail:
```
iex> [head1 | tail1] = [1, 2, 3]
[1, 2, 3]
iex> head1
1
iex> tail
[2, 3]
```
- When you want to pattern match against a variable's existing value rater than rebinding the variable use the pin operator `^`.
```
iex> x = 1
1
iex> ^x = 2
** (MatchError) no match of right hand side value: 2
```
- You cannot make function calls on the left hand side of a match
- Pattern matching in named functions:
```
defmodule Example do
  def named_function(:a = variable_a) do
    {variable_a, 1}
  end

  def named_function(:b = variable_b) do
    {variable_b, 2}
  end
end
```

## Case
- `case` allows us to compare a value against many patterns until we find a matching one. The following code will return `"This clause will match and bind x to 2"`
```
case {1, 2, 3} do
	{4, 5, 6} ->
		"This clause won't match"
	{1, x, 3} ->
		"This clause will match and bind x to 2"
	_ ->
		"This clause would match any value"
end
```
- If you want to pattern match against an existing variable, you need to use the `^` operator.
- Clauses also allow extra conditions to be specified using `when` keyword. Example clause:
```
{1, x, 3} when x > 0 ->
	"will match only when x is positive"
```
- Errors in guards (expressions started by `when` keyword) do not leak but simply make the guard fail.
- If none of the clauses match, an error is raised.

## Cond
- Useful when you want to check different conditions and find the first one that doesn't evaluate to `nil` or `false`. `cond` is equivalent to `if ... elif ... elif` in Python.
```
cond do
	1 + 1 == 5 ->
		"This will not be true"
	2 ** 2 == 22 ->
		"Nor this"
	2 + 2 == 4 ->
		"But this will"
end
```
- At least one condition must be met. Sometimes it might be necessary to add a final condition that will always be `true`, equivalent to `else` in Python.
```
true ->
	"This is always true"
```
- `cond` considers any value other than `false` and `nil` to be true.

## `if` and `unless`
- `unless` means "if not"
- Useful when you only want to check only one condition.
- They also support `else` in between `do` and `end`.
```
unless true do
	"This will never be seen"
else
	"This will always be seen"
end
```
- You cannot rebound global variables from within `if`, `case` and similar constructs. `x = x + 1` would create a new variabe within that scope with  incremented value of `x`. The original global variable would stay  the same. If you want to change its value, you must return the value from the if:
```
iex> x = 1
1
iex> x = if true do
		x + 1
	else
		x
	end
2
```

## Modules
- Elixir is a functional-programming language and requires all named functions to be defined in a module.
- The `defmodule` keyword is used to define a module. 
- All modules are available to all other modules at runtime.
- Analogous to a class in other programming languages.

## Named functions
- Defined with the `def` keyword.
- The value of the last expression is always implicitly returned. There is no "return" keyword in Elixir.
- Invoking a function is done by specifying its module and function name and passing arguments: `IO.puts("Hi!!")`
- You can set a default value of an argument with `//`. It's possible to do that for all of the arguments. In that case, you wouldn't even need to pass any arguments. The following function would return 3 if called without specifying arguments (BTW we still refer to it as sum/3 as its arity did not change).
```
def sum(a \\ 1, b \\ 1, c \\ 1) do
    a + b + c
end
```
- One liners require `,` and `:` around the `do` keyword: 
`def short_subtract(x, y), do: x - y`
- Private functions defined with `defp` keyword can only be invoked locally (within its module).
- Function declarations support guards and multiple clauses (multiple declaration of the same function)
```
defmodule Math do
  def zero?(0) do
    true
  end

  def zero?(x) when is_integer(x) do
    false
  end
end
```
- You can assign a named function to a variable using the elixir function notation prefixed with the capture operator `&`.
```
len = &String.length/1
add = &+/2
```
- The capture syntax can be used as a shortcut for creating functions. To represent first, second, ... argument use `&1`, `&2`, and so on. Example usage: `&(&1 + 1)` is exactly the same as `fn x -> x + 1 end`.

## IEx
- Run current mix project in interactive shell mode: `iex -S mix`
- You can use the pipe operator to further modify last return value.
```
iex> [1, [2], 3]
[1, [2], 3]
iex> |> List.flatten()
[1, 2, 3]
```
- Easiest way to load an elixir file to IEx: `iex test.ex` or `iex test.exs`
- Another way to load an elixir `example.ex` file to IEx without using mix, is to you compile your file using `elixirc example.ex`. That will create an `Elixir.<module>.beam` file for every defined module. Then, simply start a new `iex` session.

## Important modules
- Most built-in data types have a dedicated module: `Integer`, `String`, `List`.
- `Kernel` module offers basic arithmetics, control-flow macros and much more.
- Functions from `Kernel` module are automatically imported so you don't have to prefix them with `Kernel.`
- `Bitwise` module supports many functions for working with bits.

## Comments, documentation
- Use `#` for single-line comments.
- Use `@doc """This function returns XYZ"""` to document your functions.
- If you use triple quotation marks without special `@` prefixes, it will return `nil`. So don't use that type of comment on the last line of a function.

## Modules
- The first letter must be uppercase.
- The first letter of every function inside a module must be lowercase (`f1`) or start with an underscore (`_f2`).

## Project structure
- Elixir pojects are usually organized into three directories:
	- `_build` - contains compilation artifacts
	- `lib` - contains Elixir code (usually `.ex` files)
	- `tests` - contains tests for each file in `lib` (usually `.exs` files)

## Script files
- `.exs` extension
- To run a script file use `elixir example.exs`. The script will be compiled and loaded into memory, but no `.beam` file will be saved.
