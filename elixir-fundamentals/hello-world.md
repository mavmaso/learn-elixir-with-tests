# Hello, World

It is traditional for your first program in a new language to be [Hello, World](https://en.m.wikipedia.org/wiki/%22Hello,\_World!%22\_program).

* Create a folder wherever you like
* create a new project with: `mix new hello`&#x20;

Copy

```
* creating README.md
* creating .formatter.exs
* creating .gitignore
* creating mix.exs
* creating lib
* creating lib/foo.ex
* creating test
* creating test/test_helper.exs
* creating test/foo_test.exs

Your Mix project was created successfully.
You can use "mix" to compile it, test it, and more:

    cd foo
    mix test

Run "mix help" for more commands.
```

To run it, type `cd foo/ && mix test`.

### How it works <a href="#how-it-works" id="how-it-works"></a>

First, for more information about how mix and project setup works, [here](https://elixirschool.com/en/lessons/basics/mix).

But basically the elixir tests are made for projects, we can make simple scripts with files ending in `.exs` and run them with the command elixir `file_name.exs`, and we also have [mix-install,](https://tmr08c.github.io/2021/05/using-mix-install/) to help with more complex scripts. But our focus here is on creating app/software with TDD, so we'll go straight to projects and use mix for that.

Now about what we've just created and tested here, the mix created a project with `lib/foo.ex` and `test/foo_test.ex`.

In the Foo module:

````
```elixir
defmodule Foo do
  @moduledoc """
  Documentation for `Foo`.
  """

  @doc """
  Hello world.

  ## Examples

      iex> Foo.hello()
      :world

  """
  def hello do
    :world
  end
end

```
````

Here, we have a `hello/0` function, meaning it calls hello and requires zero arguments. In Elixir, we refer to this as a zero arity function, hence `hello/0`. Additionally, it includes documentation and a `doc test`—a straightforward type of test embedded in the documentation that can be run alongside other tests. This practice ensures that the documentation is both practical and functional. However, our focus will not be on this, as it is not related to TDD.

As for the test file, it is "mirrored" in the test folder with the suffix \_test.exs and is therefore a script.

````
```elixir
defmodule FooTest do
  use ExUnit.Case
  doctest Foo

  test "greets the world" do
    assert Foo.hello() == :world
  end
end

```
````

The test ensures that the Foo module contains a hello/0 function that returns :world. In Elixir, :world is an atom, a unique type used for internal "communication," essentially an alternative to strings. Elixir is easy to test because it includes several built-in testing tools and adheres to two fundamental rules:

* Every function returns a value, even if it’s just :ok.&#x20;
* Only two values are considered false: nil and false.

### Writing tests <a href="#how-to-test" id="how-to-test"></a>

Writing a test is just like writing a function, with a few rules

* It needs to be in a file with a name like `xxx_test.exs` on test folder.
* Use `assert/1` or any other function like from [`ExUnit.Assertions`](https://hexdocs.pm/ex\_unit/1.17.0/ExUnit.Assertions.html)

But testing isn't just about functions that ensure that other functions are working as intended, it's about documentation and architecture. So we have to improve what we already have by using BDD when writing them.

````
```elixir
defmodule FooTest do
  use ExUnit.Case

  describe "hello/0" do
    test "returns :world" do
      assert Foo.hello() == :world
    end
  end
end
```
````

BDD is a broad topic, so we'll take what we need here, which is to use the describe next to the test name to create a test suite that is more descriptive and follows the BDD idea, after all the test checks behaviour and not just the return and effects. Then run the test again, only now with the trace option: `mix test --trace`

```
Running ExUnit with seed: 913459, max_cases: 1


FooTest [test/foo_test.exs]
  * test hello/0 returns :world (0.5ms) [L#5]

Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 0 failures
```

.

### Let's get a little more complex

Whenever I discuss Test-Driven Development (TDD), which involves writing tests first and then developing the software based on those tests, the primary issue people mention is their uncertainty about how to test or what to test. Even with a simple "Hello, World!" example, this problem arises. Typically, such a function outputs to the terminal instead of returning an atom, as we are doing here. To test and capture this output, we will use `ExUnit.CaptureIO`.

````
```elixir
defmodule FooTest do
  use ExUnit.Case

  import ExUnit.CaptureIO

  describe "hello/0" do
    test "prints a message" do
      assert capture_io(fn -> Foo.hello() end) =~ "Hello, world!"
    end
  end
end

```
````

First let's read and understand the new architecture, now the `hello/0` function needs to print "Hello, world!" when called, and we don't care about the return of the function itself. Detail for the `=~` operator: This operator is used to check if a string contains a specific substring, even when the string is lengthy or includes extraneous characters.

If we run the test again:

```
Compiling 1 file (.ex)
Running ExUnit with seed: 713545, max_cases: 20

  1) test hello/0 prints a message (FooTest)
     test/foo_test.exs:7
     Assertion with =~ failed
     code:  assert capture_io(fn -> Foo.hello() end) =~ "Hello, world!"
     left:  ""
     right: "Hello, world!"
     stacktrace:
       test/foo_test.exs:8: (test)


Finished in 0.01 seconds (0.00s async, 0.01s sync)
1 test, 1 failure
```

We replace :world with `IO.puts("Hello, world!")`.

````
```elixir
defmodule Foo do
  @moduledoc """
  Documentation for `Foo`.
  """

  @doc """
  Hello world.
  """
  def hello do
    IO.puts("Hello, world!")
  end
end

```
````

We run the test again:

```
Running ExUnit with seed: 553727, max_cases: 20

.
Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 0 failures
```

We're still keeping things simple, but now let's add some complexity by personalizing the `hello/0` function with someone's name. To achieve this, we'll use string interpolation, which allows us to embed the value of a variable within a string. In Elixir, always use double quotes `"` to start and end a string, and use `#{}` to interpolate variables or values within the string.

````
```elixir
defmodule FooTest do
  use ExUnit.Case

  import ExUnit.CaptureIO

  describe "hello/1" do
    test "prints a message with a name" do
      name = "Marcos"
      assert capture_io(fn -> Foo.hello(name) end) =~ "Hello, world! from #{name}"
    end
  end
end
```
````

If we run the test:&#x20;

```
1) test hello/1 prints a message (FooTest)
     test/foo_test.exs:7
     ** (UndefinedFunctionError) function Foo.hello/1 is undefined or private. Did you mean:

           * hello/0
     
     code: assert capture_io(fn -> Foo.hello(name) end) =~ "Hello, world! from #{name}"
     stacktrace:
       (foo 0.1.0) Foo.hello("Marcos")
       (ex_unit 1.17.0) lib/ex_unit/capture_io.ex:315: ExUnit.CaptureIO.do_capture_gl/2
       (ex_unit 1.17.0) lib/ex_unit/capture_io.ex:273: ExUnit.CaptureIO.do_with_io/3
       (ex_unit 1.17.0) lib/ex_unit/capture_io.ex:142: ExUnit.CaptureIO.capture_io/1
       test/foo_test.exs:9: (test)

Finished in 0.02 seconds (0.00s async, 0.02s sync)
1 test, 1 failure
```

We reach the core of TDD: creating demand, in this case, errors with the tests, to solve them. Here, we encounter our first issue: the `hello/1` function doesn't exist.

````
```elixir
defmodule Foo do
  @moduledoc """
  Documentation for `Foo`.
  """

  @doc """
  Hello world.
  """
  def hello(name) do
    IO.puts("Hello, world! from #{name}")
  end
end
```
````

#### Discipline <a href="#discipline" id="discipline"></a>

Let's go over the cycle again

* Write a test
* Make the compiler pass
* Run the test, see that it fails and check the error message is meaningful
* Write enough code to make the test pass
* Refactor

On the face of it this may seem tedious but sticking to the feedback loop is important.

Not only does it ensure that you have _relevant tests_, it helps ensure _you design good software_ by refactoring with the safety of tests.

### Wrapping up <a href="#wrapping-up" id="wrapping-up"></a>

Who knew you could get so much out of `Hello, world`?

By now you should have some understanding of:

#### Some of Elixir's syntax around <a href="#some-of-gos-syntax-around" id="some-of-gos-syntax-around"></a>

* Writing tests
* Declaring functions, with arguments/arity
* Declaring variables

#### The TDD process and _why_ the steps are important <a href="#the-tdd-process-and-why-the-steps-are-important" id="the-tdd-process-and-why-the-steps-are-important"></a>

* _Write a failing test and see it fail_ so we know we have written a _relevant_ test for our requirements and seen that it produces an _easy-to-understand description of the failure_
* Writing the smallest amount of code to make it pass so we know we have working software
* _Then_ refactor, backed with the safety of our tests to ensure we have well-crafted code that is easy to work with

In our case, we've gone from `hello/0` to `hello/1` and then to `hello/2` in small, easy-to-understand steps.

Of course, this is trivial compared to "real-world" software, but the principles still stand. TDD is a skill that needs practice to develop, but by breaking problems down into smaller components that you can test, you will have a much easier time writing software.

\


