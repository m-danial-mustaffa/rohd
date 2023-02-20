## Content

- [Basic logic](./00-Basic_Logic.md#basic-logic)
  * [Logic, Logic Value, Width](./00-Basic_Logic.md#logic-logic-value-width)
    * [Exercise 1](./00-Basic_Logic.md#exercise-1)
  * [Logic Gate: Part 1](./00-Basic_Logic.md#logic-gate-part-1)
    * [Assignment, Logical, Mathematical, Comparison Operations](./00-Basic_Logic.md#assignment-logical-mathematical-comparison-operations)
      * [Assignment](./00-Basic_Logic.md#assignment)
      * [Logical, Mathematical, Comparison Operations](./00-Basic_Logic.md#logical-mathematical-comparison-operations)
  * [Logic Gate: Part 2](./00-Basic_Logic.md#logic-gate-part-2)
    * [Non-synthesizable signal deposition (put)](./00-Basic_Logic.md#non-synthesizable-signal-deposition-put)
  * [Logic Gate: Part 3](./00-Basic_Logic.md#logic-gate-part-3)
    * [Exercise 2](./00-Basic_Logic.md#exercise-2)
- [Constants](./00-Basic_Logic.md#constants)
  * [Exercise 3](./00-Basic_Logic.md#exercise-3)
- [Bus Ranges and Swizzling](./00-Basic_Logic.md#bus-ranges-and-swizzling)

## Learning Outcome

Now that you have set up your development environment, let's get our hands dirty and start writing some ROHD code in this chapter.

In this chapter:

- You will learn about the fundamentals of ROHD variables, including how to define a logic, logic value, and initialize width for logic. You will learn by creating a simple 2-inputs and 1-output AND logic gate. In the process of creating the logic gate, you will understand how to use assignment, logical, mathematical, and comparison operations. You will also learn how to use non-synthesizable signal deposition (put) to send signals to the simulator to test your created logic gate.

- You will learn how to define constants, bus ranges, and swizzling to slice or access a range of bits from a bus.

# Basic Logic

## Logic, Logic Value, Width

Like any programming language, ROHD has its own data types, which include `Logic` and `LogicValue`. `Logic` is fundamental to creating signals.

Note that in Dart, variable names are typically written in camelcase, such as `aSignal` and `thisIsVariable`. Visit this page to learn more [https://dart.dev/guides/language/effective-dart/style](https://dart.dev/guides/language/effective-dart/style)

```dart
// 1-bit unnamed signal. 
Logic unamedSignal = Logic();

// 8-bit bus named 'b'.
Logic bus = Logic(name: 'b', width: 8);

// You can use toString() method to check for your signals details.
print(unamedSignal.toString());
```

In the example above, we can see that creation of `Logic` signals involved instantiate a `Logic` that can received `name` and `width` as an argument.

### Exercise 1:

1. Create a 3-bit bus signal named `threeBitBus`.
2. Print the output of the signal. Explain what you see. Is there enough information in the output to verify that you have created the correct signal?

Now that we've learned how to create a Logic signal, let's explore how to access its value.

To access the value of a Logic signal, you can simply call its `value` property. You can convert the value to an integer using the `toInt()` method, but note that this is only valid for signals that don't have any x or z bits. If the signal has more bits than can fit in a 64-bit integer, you'll need to use the `toBigInt()` method instead.

The value of a Logic signal is of type `LogicValue`, which has pre-defined constant bit values such as `x`, `z`, `one`, and `zero`.

Let's take a look at an example of getting the value of the threeBitBus signal that we created earlier.

```dart
Logic bus = Logic(name: 'threeBitBus', width: 3);

// .put() is one way to simulate a signal on a Logic signal that has been created. We will come back to this in later section.
bus.put(1);

// Obtain the value of bus.
LogicValue busVal = bus.value; 

// Obtain the value of bus in Int
int busValInt = bus.value.toInt(); 

// If you set your bus width larger than 64 bits. 
// You have to use toBigInt().
Logic bigBus = Logic(name: 'b', width: 65);

bigBus.put(BigInt.parse("9223372036854775808"));
LogicValue bigBusValBigInt = bigBus.value.toBigInt();

// output: 8'h1.
print(busVal);

// output: 1.
print(busValInt);

// output: 9223372036854775808.
print(bigBusValBigInt);
```

## Logic Gate: Part 1

By now, you have learned about `Logic` and `LogicValue`. Let's now dive into our first tutorial on Logic gate. Suppose we want to build a 2-input Logic `AND` gate.

![And Gate](./assets/And_gate.png)

To create our two-input Logic `AND` gate, we need to declare two input signals and one output signal, which means we will need to create a total of three Logic signals.

```dart
import 'package:rohd/rohd.dart';

void main() {
  // Create input and output signals
  final a = Logic(name: 'input_a');
  final b = Logic(name: 'input_b');
  final c = Logic(name: 'output_c');
}
```

That's all! We have created all the ports required. Next, let's take a look at the operators in ROHD.

## Assignment, Logical, Mathematical, Comparison Operations

### Assignment

To assign the value of one signal to another signal, use the `<=` operator. This is a hardware synthesizable assignment that connects two wires together.

Let's take a look at an example of how to assign a Logic signal `a` to signal `b`.

```dart
Logic a = Logic(name: 'signal_a');
Logic b = Logic(name: 'signal_b');

// In this case, b is connected to a which means they will have the same value.
a <= b;
```

### Logical, Mathematical, Comparison Operations

In ROHD, we have operators that are similar to those in SystemVerilog. This makes it easier for users to learn and pick up the language.

```dart
a_bar     <=  ~a;      // not
a_and_b   <=  a & b;   // and
a_or_b    <=  a | b;   // or
a_xor_b   <=  a ^ b;   // xor
and_a     <=  a.and(); // unary and
or_a      <=  a.or();  // unary or
xor_a     <=  a.xor(); // unary xor
a_plus_b  <=  a + b;   // addition
a_sub_b   <=  a - b;   // subtraction
a_times_b <=  a * b;   // multiplication
a_div_b   <=  a / b;   // division
a_mod_b   <=  a % b;   // modulo
a_eq_b    <=  a.eq(b)  // equality              NOTE: == is for Object equality of Logic's
a_lt_b    <=  a.lt(b)  // less than             NOTE: <  is for conditional assignment
a_lte_b   <=  a.lte(b) // less than or equal    NOTE: <= is for assignment
a_gt_b    <=  (a > b)  // greater than          NOTE: careful with order of operations, > needs parentheses in this case
a_gte_b   <=  (a >= b) // greater than or equal NOTE: careful with order of operations, >= needs parentheses in this case
answer    <=  mux(selectA, a, b) // answer = selectA ? a : b
```

Great, now that you've learned all about our operators, let's continue our journey and create an `AND` gate. We can use the `&` operator that we learned earlier to create an `AND` logic gate.

## Logic Gate: Part 2

```dart
import 'package:rohd/rohd.dart';

void main() {
  // Create input and output signals.
  final a = Logic(name: 'input_a');
  final b = Logic(name: 'input_b');
  final c = Logic(name: 'output_c');

  // Create an AND logic gate.
  // This assign c to the result of a AND b.
  c <= a & b;
}
```

Congratulations! You have created your logic gate. Let's move on to the next section to test our gate.

### Non-synthesizable signal deposition (put)

Do you still remember the `put()` function that was used in the previous section? It is used to send a simulated signal to the input `Logic`.

For testbench code or other non-synthesizable code, you can use `put` or `inject` on any `Logic` to deposit a value on the signal. The two functions have similar behavior, but `inject` is a shorthand for calling `put` inside `Simulator.injectAction`, which allows the deposited change to propagate within the same `Simulator` tick. Generally, you will want to use `inject` for testbench interaction with a design.

Now let's see an example of how to deposit signals for testing, using both `put` and `inject`.

```dart
b = Logic(width:4);

// you can put an integer directly into a signal.
a.put(4);

// Use only with Simulator tick.
a.inject(4);
```

## Logic Gate: Part 3

Now, we can test our logic gate with the simulator.

```dart
import 'package:rohd/rohd.dart';

void main() {
  // Create input and output signals
  final a = Logic(name: 'input_a');
  final b = Logic(name: 'input_b');
  final c = Logic(name: 'output_c');

  // Create an AND logic gate
  // This assign c to the result of a AND b
  c <= a & b;

  // let try with simple a = 1, b = 1
  // a.put(1);
  // b.put(1);
  // print(c.value.toInt());

  // Let build a truth table
  for (int i = 0; i <= 1; i++) {
    for (int j = 0; j <= 1; j++) {
      a.put(i);
      b.put(j);
      print("a: $i, b: $j c: ${c.value.toInt()}");
    }
  }
}
```

Congratulations!!! You have successfully build your first gate! 

### Exercise 2:

1. Build OR or NOR or XOR gate using ROHD.

# Constants

In ROHD, constants can often be inferred by ROHD automatically, but can also be explicitly defined using `Const`, which extends `Logic`.

```dart
// a 16 bit constant with value 5.
var x = Const(5, width:16);
```

### Exercise 3:

1. Create a constant of value 10 and assign to a Logic input.

# Bus Ranges and Swizzling

In the previous module, we learned about the `width` property of `Logic`. Now, we can perform operations like slicing and swizzling on `Logic` values.

We can access multi-bit buses using single bits, ranges, or by combining multiple signals. Additionally, we can use operations like slicing and swizzling on `Logic` values.

```dart
import 'package:rohd/rohd.dart';

void main() {
  final a = Logic(width: 4);
  final b = Logic(width: 8);
  final c = Const(7, width: 5);
  final d = Logic();

  // assign b to the bottom 3 bits of a
  // input = [1, 1, 1, 0], output = 110
  a.put(bin('1110'));
  print('a.slice(2, 0): ${a.slice(2, 0).value.toString(includeWidth: false)}');

  b.put(bin('11000100'));
  print('a[7]: ${b[7].value.toString(includeWidth: false)}');
  print('a[0]: ${b[0].value.toString(includeWidth: false)}');

  // assign d to the top bit of a
  // construct e by swizzling bits from b, c, and d
  // here, the MSB is on the left, LSB is on the right
  d <= b[7];

  // value:
  // swizzle: d = [1] (MSB), c = [00111], a = [1110] (LSB) , e = [1 00111 1110] = [d, c, a]
  final e = Logic(width: d.width + c.width + a.width);
  e <= [d, c, a].swizzle();

  print('e: ${e.value.toString(includeWidth: false)}');

  // alternatively, do a reverse swizzle (useful for lists where 0-index is actually the 0th element)
  // here, the LSB is on the left, the MSB is on the right
  // right swizzle: d = [1] (MSB), c = [00111], a = [1110] (LSB) , e = [1110 00111 1] - [a, c, d]
  final f = Logic(width: d.width + c.width + a.width);
  f <= [d, c, a].rswizzle();
  print('f: ${f.value.toString(includeWidth: false)}');
}
```

ROHD does not support assignment to a subset of a bus. That is, you cannot do something like `e[3] <= d`. Instead, you can use the withSet function to get a copy with that subset of the bus assigned to something else. This applies for both Logic and LogicValue. For example:

```dart
// reassign the variable `e` to a new `Logic` where bit 3 is set to `d`
e = e.withSet(3, d);
```

----------------
2023 February 14
Author: Yao Jing Quek <<yao.jing.quek@intel.com>>

 
Copyright (C) 2021-2023 Intel Corporation  
SPDX-License-Identifier: BSD-3-Clause