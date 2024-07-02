# Basic Types

&#x20;First basic type: String ...

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
    name = "Erica "

    assert Basic.format(name) == "erica"
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

### Write enough code to make it pass <a href="#write-enough-code-to-make-it-pass" id="write-enough-code-to-make-it-pass"></a>

### Refactor <a href="#refactor" id="refactor"></a>

### Repeat for new requirements <a href="#repeat-for-new-requirements" id="repeat-for-new-requirements"></a>

### Wrapping up <a href="#wrapping-up" id="wrapping-up"></a>
