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

  test "format string" do
    name = :erica

    assert Basic.format(name) == "Erica"
  end
end
```

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

Let's analyze what we have here: `name = :erica`, a variable `name` with the value as an atom. How do we know this? In Elixir, the type can be identified by its "shape," and in this case, since it starts with a colon, we can tell that it's an atom, which is a very particular type in Elixir. It acts similarly to enums in other languages.

However, for the test, we want it to be formatted as `"Erica"`. Visually, we can see that it’s a string since it’s enclosed in double quotes. If it were in single quotes, it would be a charlist, but we rarely use that.

Now, let's write the code to achieve this:

```elixir
defmodule Basic do
  def format(name) do
   name
  end
end

ExUnit.start()

defmodule BasicTest do
  use ExUnit.Case

  test "format string" do
    name = :erica

    assert Basic.format(name) == "Erica"
  end
end
```

If we run it again with: `elixir basic.exs`

```
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

A victory! We've already fixed the initial error.

### Write enough code to make it pass <a href="#write-enough-code-to-make-it-pass" id="write-enough-code-to-make-it-pass"></a>

Para solucionar isso iremos usar o |>, pipe operator, para que possamos encadear o resultados das funçoes e transformar o atom na string e  deixar ele com a primeira letra em caixa alta.

```elixir
defmodule Basic do
  def format(name) do
   name
   |> Atom.to_string()
   |> String.capitalize()
  end
end

ExUnit.start()

defmodule BasicTest do
  use ExUnit.Case

  test "format string" do
    name = :erica

    assert Basic.format(name) == "Erica"
  end
end
```

E ao rodar o teste de novo:  : `elixir basic.exs`

```
.
Finished in 0.02 seconds (0.02s on load, 0.00s async, 0.00s sync)
1 test, 0 failures
```

### Refactor <a href="#refactor" id="refactor"></a>

Agora vamos usar outros dois tipos muito bem comum: numeros e booleans.&#x20;

```
// Some code
```



### Repeat for new requirements <a href="#repeat-for-new-requirements" id="repeat-for-new-requirements"></a>

### Wrapping up <a href="#wrapping-up" id="wrapping-up"></a>
