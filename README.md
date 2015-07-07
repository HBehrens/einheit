# einheit

Einheit means unit in German.

Einheit is a Nim unit testing library inspired by Python's unit tests. Nim's unittest library is good, but I wanted something a little more "Nim" feeling. I also really like Python's unittest module and thought it would be nice to have something similar in Nim. Also, unittest doesn't have much documentation on how to use it, and it's pretty bare bones, so I wanted a little more functionality and documentation.

The benefit of the macro style I chose means that you can document your tests nicely as well :)

### Description
testSuite is a compile-time macro that allows a user to easily define tests and run them.

Methods are used for inheritance, so if you want to derive a test suite, then you have to make sure the base suite uses methods for the tests that you want to derive.

If you don't want inheritance, you can just use procs.

A special proc/method is called setup(). The macro will inject this method/proc if it doesn't exist and it will be called before running the test suite.

Test methods/procs to be run are prefixed with "test" in the method/proc name. This is so that you can write tests that call procs that do other things and won't be run as a test.

For each suite method/proc, an implicit variable called "self" is added. This lets you access the testSuite in an OO kind of way.

### Installation

Install with nimble!

```bash
nimble install einheit
```

### Usage

```nim
import einheit

testSuite SuiteName of TestSuite:

  var
    suiteVar: string
    testObj: int

  method setup()=
    ## do setup code here
    self.suiteVar = "Testing"
    self.testObj = 90

  method testAddingString()=
    ## adds a string to the suiteVar
    self.suiteVar &= " 123"
    self.assertEqual(self.suiteVar, "Testing 123")

  proc raisesOs()=
    # This proc won't be invoked as a test, it must begin with "test" in lowercase
    raise newException(OSError, "Oh no! OS malfunction!")
  
  method testRaises()=
    # Two ways of asserting
    self.assertRaises OSError:
      self.raisesOs()

    self.assertRaises(OSError, self.raisesOs())

  method testTestObj()=
    self.assertTrue(self.testObj == 90)

  method testMoreMore()=
    self.assertFalse("String" != "String")

  when isMainModule:
    einheit.runTests()
```

You can also find examples in the [test.nim](test.nim) file, including inheritance.


Output of running

```bash
nim c -r test.nim
```

is this:

```
[Running] UnitTests

[OK]     testForB
[Failed] testForC
  Condition: assertTrue(c, 1)
  Reason: c == 0; 0 != 1
  Location: test.nim; line 24

[1/2] tests passed.


[Running] UnitTestsNew

[OK]     testTestObj
[OK]     testStuff
[OK]     testMore
[OK]     testMoreMore

[4/4] tests passed.


[Running] TestInherit

[OK]     testTestObj
[OK]     testStuff
[OK]     testMore
[OK]     testMoreMore
[OK]     testRaises

[5/5] tests passed.


[Running] MoreInheritance

[Failed] testTestObj
  Condition: assertTrue(self.testObj == 90)
  Reason: (self.testObj == 90) == false
  Location: test.nim; line 36
[OK]     testStuff
[OK]     testMore
[OK]     testMoreMore
[OK]     testRaises
[OK]     testTestObj
[OK]     testNewObj

[6/7] tests passed.
```

Notice that on failure, the test runner gives some useful information about the test in question. This is useful for determining why the test failed.
