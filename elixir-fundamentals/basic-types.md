# Basic Types

Elixir have few types:

* Atom
* String & Charlist
* Numbers
* List
* Tuples
* Maps
* Struct
* Keyword List
* PID, Datatime, etc

&#x20;The last two can be considered somewhat more advanced. Therefore, in today’s lesson, we will focus on Atoms, Strings, and Numbers.

### Write the test first <a href="#write-the-test-first" id="write-the-test-first"></a>

&#x20;This time, let's use a script to learn about type.

* Create a new folder and a file name `basic.exs`

```elixir
defmodule Basic do
  
end

ExUnit.start()

defmodule BasicTest do
  use ExUnit.Case

  test "format to string" do
    name = :erica

    assert Basic.format(name) == "Erica"
  end
end
```

We expect a function `format/1` to take the atom `:erica` and return the string `"Erica"`.

### Try to run the test <a href="#try-to-run-the-test" id="try-to-run-the-test"></a>

In this case, to run the test: `elixir basic.exs`

```
  1) test format string (BasicTest)
     basic.exs:10
     ** (UndefinedFunctionError) function Basic.format/1 is undefined or private
     code: assert Basic.format(name) == "erica"
     stacktrace:
       Basic.format("Erica ")
       basic.exs:13: (test)


Finished in 0.07 seconds (0.06s on load, 0.00s async, 0.01s sync)
1 test, 1 failure
```

### Write the minimal amount of code for the test to run and check the failing test output <a href="#write-the-minimal-amount-of-code-for-the-test-to-run-and-check-the-failing-test-output" id="write-the-minimal-amount-of-code-for-the-test-to-run-and-check-the-failing-test-output"></a>

Let’s analyze what we have so far.

We defined a variable:

```elixir
name = :erica
```

In Elixir, `:erica` is an **atom**. We can tell this by its shape: it starts with a colon (`:`). This is how atoms are written in Elixir.\
Atoms are constants whose value is their own name, similar, but not the same, to enums in other languages.

Now look at the test:

```elixir
assert Basic.format(name) == "Erica"
```

The expected output is `"Erica"`, a **string** — we know that because it's surrounded by **double quotes**.\
In Elixir, double quotes define **strings**, while single quotes define **charlists**, which are rarely used unless you're working with older Erlang-style APIs.

So we’re asking Elixir to convert the atom `:erica` into the string `"Erica"`.

Before we can fix the test, we need to make sure it actually runs.\
Let's define a `format/1` function that just returns the input, without doing any transformation:

```elixir
defmodule Basic do
  def format(name) do
   name
  end
end

ExUnit.start()

defmodule BasicTest do
  use ExUnit.Case

  test "format to string" do
    name = :erica

    assert Basic.format(name) == "Erica"
  end
end
```

If we run it again with: `elixir basic.exs`

```bash
 1) test format string (BasicTest)
     basic.exs:12
     Assertion with == failed
     code:  assert Basic.format(name) == "Erica"
     left:  :erica
     right: "Erica"
     stacktrace:
       basic.exs:15: (test)


Finished in 0.03 seconds (0.03s on load, 0.00s async, 0.00s sync)
1 test, 1 failure
```

This is good news! The test **compiles** and **runs**, but it fails — just as we want at this stage.

The error message tells us:

* The **left** value (`:erica`) is the result of calling `Basic.format(name)`
* The **right** value (`"Erica"`) is what the test expected

We’ve now reached the **red** step of the TDD cycle: we wrote a failing test that clearly expresses the behavior we want.

Next, we’ll make it pass.

### Write enough code to make it pass <a href="#write-enough-code-to-make-it-pass" id="write-enough-code-to-make-it-pass"></a>

Let’s implement just enough code to make the test pass.

We need to:

* Convert the atom to a string (`Atom.to_string/1`)
* Capitalize the first letter (`String.capitalize/1`)



```elixir
defmodule Basic do
  def format(name) do
    stringify = Atom.to_string(name)
    String.capitalize(stringify)
  end
end

ExUnit.start()

defmodule BasicTest do
  use ExUnit.Case

  test "format to string" do
    name = :erica

    assert Basic.format(name) == "Erica"
  end
end
```

Now, run the test again: `elixir basic.exs`

```bash
.
Finished in 0.02 seconds (0.02s on load, 0.00s async, 0.00s sync)
1 test, 0 failures
```

Green!

### Refactor <a href="#refactor" id="refactor"></a>

Now that the test passes, let’s improve the code.

We want to transform an atom like `:erica` into a string `"Erica"`. We can do that in two steps:

1. Convert the atom to a string using `Atom.to_string/1`, which would return `"erica"`.
2. Capitalize the first letter using `String.capitalize/1`, which would return `"Erica"`.

We could write this like a sequence of function calls:

```elixir
String.capitalize(Atom.to_string(name))
```

That works just fine, but Elixir offers a more **readable** and **idiomatic** way to chain functions using the **pipe operator (`|>`)**.

The pipe operator (`|>`) takes the result of the expression on its left and **passes it as the first argument** to the function on its right.

So this:

```elixir
defmodule Basic do
  def format(name) do
   name
   |> Atom.to_string()
   |> String.capitalize()
  end
end
```

It’s a bit like saying:

* Take `name` → pass it to `Atom.to_string/1`
* Take the result of that → pass it to `String.capitalize/1`

This makes the data **flow** from top to bottom in a clean and readable way. In Elixir, this style is very common and encouraged — especially when you’re transforming data step by step.

```bash
.
Finished in 0.02 seconds
1 test, 0 failures
```

✅ Still green! And now much easier to read.

### Repeat for new requirements <a href="#repeat-for-new-requirements" id="repeat-for-new-requirements"></a>

Now let’s introduce another basic type: numbers.\
We'll write a function to check if the result of adding two numbers is even.

Update the test:

```elixir
defmodule BasicTest do
  use ExUnit.Case

  test "format string" do
    name = :erica

    assert Basic.format(name) == "Erica"
  end

  test "check if sum is even" do
    a = 1
    b = 3

    assert Basic.result_even?(a, b)
    assert Basic.result_even?(a, b + 1) == false
  end
end
```

💡 In Elixir, functions that return booleans are usually named with a `?` suffix.

```
** (UndefinedFunctionError) function Basic.result_even?/2 is undefined
```

Let’s fix that:

```elixir
defmodule Basic do
  def format(name) do
   name
   |> Atom.to_string()
   |> String.capitalize()
  end

  def result_even?(a, b) do
    rem(a + b, 2) == 0
  end
end
```

Run the test:

```bash
..
Finished in 0.03 seconds (0.03s on load, 0.00s async, 0.00s sync)
2 tests, 0 failures
```

### Wrapping up <a href="#wrapping-up" id="wrapping-up"></a>

In this chapter, we:

* Explored **atoms** (`:erica`)
* Converted them into **strings**
* Capitalized strings using `String.capitalize/1`
* Introduced **numbers**
* Performed a numeric operation (`a + b`) and used `rem/2` to check for evenness
* Used boolean-returning functions with the `?` naming convention
* Practiced chaining function calls with the **pipe operator** (`|>`)

All of this was done using test-driven development: writing the test first, watching it fail, making it pass, and expanding requirements.

In the next chapter, we’ll dive into Elixir's **collections**: **lists**, **keyword list**, **tuples**, and **maps** and **structs**.

...
