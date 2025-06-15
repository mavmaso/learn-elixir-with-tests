# Advenced Types

Elixir has a few basic types, but some deserve a bit more attention — not because they are difficult, but because they are central to how we model and work with data. We call them **advanced types**, and you'll use them just as often as the basics from the previous chapter.

In this lesson, we’ll focus on the types that can **group values** — what we’d usually call “collections.” In Elixir, these are part of the **Enum**, and it's very common to group data using them.

Because Elixir is dynamically typed, you can technically mix numbers, strings, atoms, etc., in a collection — but it’s considered good practice to group items of the same kind. Also, remember: give plural names to your collections to clarify their intent.

Let’s quickly summarize what we’ll explore:

* **Tuples**: Like fixed-length arrays in other languages. They're often used to **wrap** a result (e.g. `{:ok, 1}` or `{:error, :not_found}`).
* **Lists & Keyword Lists**: Elixir uses linked lists instead of arrays, which improves performance in many functional patterns. `Keyword Lists` are special: they’re just lists of tuples, but they act like maps in some contexts.
* **Maps & Structs**: These are core to Elixir’s data-driven nature. You’ll use them everywhere. Structs are just maps with a bit of structure and safety on top.

Let’s dive in.

### Write the test first <a href="#write-the-test-first" id="write-the-test-first"></a>

Let’s start by writing a script in `advanced.exs`:

```elixir
defmodule Advanced do
end

ExUnit.start()

defmodule AdvancedTest do
  use ExUnit.Case

  test "checks if the number is in the list" do
    assert Advanced.contains(2) == {:ok, 2}
    assert Advanced.contains(10) == {:error, :not_found}
    assert Advanced.contains(-1) == {:error, :unprocessed}
    assert Advanced.contains(nil) == {:error, :unprocessed}
    assert Advanced.contains("3") == {:error, :unprocessed}
  end
end

```

We're testing whether a value is found in a list, but **only** if the value is a positive integer.

### Try to run the test <a href="#try-to-run-the-test" id="try-to-run-the-test"></a>

```bash
elixir advanced.exs
```

You’ll get an error like:

```bash
** (UndefinedFunctionError) function Advanced.contains/2 is undefined or private
```

Perfect! Time to implement it.

### Write the minimal amount of code for the test to run and check the failing test output <a href="#write-the-minimal-amount-of-code-for-the-test-to-run-and-check-the-failing-test-output" id="write-the-minimal-amount-of-code-for-the-test-to-run-and-check-the-failing-test-output"></a>

Let’s define the function:

```elixir
defmodule Advanced do
  @values [1, 2, 3, 4]

  def contains(_value), do: raise "not implemented yet"
end
```

WIP - RAISE  and @var

Run it again:

```bash
1) test checks if a list contains a valid integer (AdvancedTest)
     advanced.exs:19
     ** (RuntimeError) not implemented yet
     code: assert Advanced.contains(values, 2) == {:ok, 2}
     stacktrace:
       advanced.exs:2: Advanced.contains/2
       advanced.exs:22: (test)

```

This confirms our function is being invoked.

### Write enough code to make it pass <a href="#write-enough-code-to-make-it-pass" id="write-enough-code-to-make-it-pass"></a>

We can use f**unction pattern matching together with guard clauses:**

```elixir
defmodule Advanced do
  @values [1, 2, 3, 4]

  def contains(value) when not is_integer(value) or value <= 0, do: {:error, :unprocessed}

  def contains(value) when value in @values, do: {:ok, value}

  def contains(_value), do: {:error, :not_found}
end
```

But we can do even better.

### Refactor <a href="#refactor" id="refactor"></a>

Now let’s rewrite a working version that is more elegant and functional. This time, that can check any list.

```elixir
defmodule AdvancedTest do
  use ExUnit.Case

  test "checks if a list contains a valid integer" do
    values = [1, 2, 3, 4]

    assert Advanced.contains(values, 2) == {:ok, 2}
    assert Advanced.contains(values, 10) == {:error, :not_found}
    assert Advanced.contains(values, -1) == {:error, :unprocessed}
    assert Advanced.contains(values, nil) == {:error, :unprocessed}
    assert Advanced.contains(values, "3") == {:error, :unprocessed}
  end
end
```

```elixir
defmodule Advanced do
  def contains(_, value) when not is_integer(value) or value <= 0, do: {:error, :unprocessed}

  def contains(list, value), do: get_value(Enum.member?(list, value), value)

  defp get_value(true, value), do: {:ok, value}
  defp get_value(false, _), do: {:error, :not_found}
end
```

Let’s break this down:

* The first clause handles **invalid input** using a guard: `when not is_integer(value) or value <= 0`. In Elixir, we often use guards to validate arguments directly at the function definition level.
* The second clause delegates to a helper function `get_value/2`, passing the result of `Enum.member?/2`, which checks if the value is present in the list.
* The helper function `get_value/2` is **private** (declared with `defp`) because it's only used internally, and we don't want to expose it outside the module.
* By using **pattern matching on function heads**, we avoid branching logic (`if`, `case`) and instead express each scenario explicitly and clearly.

This approach is idiomatic in Elixir: **functions are separated by intention**, not controlled by nested conditionals.

Run the test again:

```elixir
.
Finished in 0.03 seconds
1 tests, 0 failures
```

Success! 🎉

### Repeat for new requirements <a href="#repeat-for-new-requirements" id="repeat-for-new-requirements"></a>

Now we want to **return a struct** instead of just a tuple.

This will help us:

* Introduce how to define and use structs
* Show how they’re just a specialized form of maps

Let’s create a struct:

```elixir
defmodule Result do
  defstruct given: nil, found?: false, error_code: nil  
end
```

And now update our tests:

```elixir
defmodule AdvancedTest do
  use ExUnit.Case

  test "returns a Result struct" do
    values = [1, 2, 3, 4]

    assert %Advanced.Result{given: 2, found?: true} = Advanced.contains(values, 2)
    assert %Advanced.Result{given: 10, found?: false} = Advanced.contains(values, 10)
    assert %Advanced.Result{given: -1, error_code: :unprocessed} = Advanced.contains(values, -1)
    assert %Advanced.Result{given: nil, error_code: :unprocessed} = Advanced.contains(values, nil)
    assert %Advanced.Result{given: "3", error_code: :unprocessed} = Advanced.contains(values, "3")
  end
end
```

So far, we've been returning tuples like `{:ok, value}` or `{:error, reason}`. This is already a good pattern and very common in Elixir. However, in many Elixir applications — especially as complexity grows — we benefit from returning richer, named data structures.

This is a good moment to introduce two fundamental Elixir types: **maps** and **structs**.

**Why maps and structs?**

* A **map** is a key-value data structure similar to dictionaries or hashes in other languages.
* A **struct** is a special kind of map with a fixed set of keys, defined via a `defstruct`. Structs give more shape and meaning to your data.

Let’s say we want every response from `contains/2` to include:

* The original `given` value,
* A result indicator: `found?: true/false`, or
* An error, like `:not_found` or `:unprocessed`.

Instead of returning different tuple formats, we can wrap everything into a `%Result{}` struct. This improves readability, enforces consistent shape, and models our intent more clearly, but for a real project it is a little overkill.&#x20;

Notice how we're using **pattern matching on the map**, but we’re not matching _all_ keys. This is possible because Elixir allows **partial matching** on maps — a powerful way to verify just the relevant fields.

This style makes our tests:

* More **readable**,
* More **flexible** to internal implementation changes,
* More **Elixir-ish**, by leaning into its pattern matching strengths.

Now let’s update the implementation to match this new shape.

```elixir
defmodule Advanced do
  defmodule Result do
    defstruct given: nil, found?: false, error_code: nil
  end

  def contains(_, value) when not is_integer(value) or value <= 0 do
    %Result{given: value, found?: false, error_code: :unprocessed}
  end

  def contains(list, value) do
    %Result{given: value, found?: Enum.member?(list, value)}
  end
end
```

### Wrapping up <a href="#wrapping-up" id="wrapping-up"></a>

In this chapter, we explored more advanced types:

* Tuples to signal success/error, just like `Result` in Rust
* Lists and how Elixir encourages working with head/tail over index-based access
* Keyword lists — and how they are syntactic sugar over list of tuples
* Maps as key-value stores
* Structs as data-focused alternatives to objects

You’ve also learned:

* How to write pure functions
* How to write clean, idiomatic Elixir without using `if` or `case`
* How to evolve from simple outputs to richer, typed data using structs

Next up: we'll explore how Elixir handles decisions and control flow — not just with `if` and `case`, but with the full power of pattern matching and recursion.
