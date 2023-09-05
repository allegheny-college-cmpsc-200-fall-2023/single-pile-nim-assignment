# CMPSC 200: Single-Pile Nim

| Date              |          |
|:------------------|:---------|
| 5 September 2023 | Assigned  |
| 12 September 2023 | Due, start of class       |
| Status           | [![GatorGrader](../../actions/workflows/main.yml/badge.svg)](../../actions/workflows/main.yml) |

## Introduction

We've learned a bit about the mysteries of our computing platform, the CARDIAC. This week we'll learn a bit more about implementing
the `JMP` opcode (`8`), the `SFT` (shift) opcode (`4`) and some advanced ways to use the machine in advance of our big upgrade from
paper to silicon.

This week's work contains four (4) programs that we'll work with over the course of the week, learning more about these advanced
instructions and applications in order to create a greedy mining robot, the Nim. These programs are:

* Magic `9` decoder
* Subroutine-based version of our array adder
* High-precision calculator for 6-digit numbers
* Single-pile Nim miner

### Base camp

#### Magic `9` decoder

Base security is very particular about who they let in to mission control. Previously, they've just watched the door. Given that 
we're starting to really get cooking (i.e. launching rockets), it's probably time for a more secure door system. You're the newest
folks to show up, so you get the job.

Your task is to write the verification program such that you can calculate the appropriate response to a challenge number. For example:

* prospective entrant keys in a 3-digit number _without repeating digits_
* the machine should reverse the number and subtract it, producing the correct answer to the challenge
* the result is the number that anyone who _knows the code sequence_ should be able ot enter in order to gain access to the room

We call this a Magic `9` decoder because the answer always features:

* a `9` in the middle
* the end digits of the resulting `3` digit number will add to `9`

Loose lips sink rockets. Er, ships.

We'll go with rockets. Don't tell anyone the code sequence.

Complete this work in [src/decoder.cardiac](src/decoder.cardiac)

#### Subroutine-based array adder

Some of the other space nerds want to use our adder. That's great, except they don't want the sequence to be `-1` terminated, but
they'd like to use our memory locations and adding technology anyway. This means we have to break out our adder into some subroutines
(think functions) so that others can potentially work on the same (or a connected system) and use _parts_ of the system. We have to
do this because, well, if we don't some math nerds will get mad.

Complete this work in [src/subroutine.cardiac](src/subroutine.cardiac)

#### High-precision calculator

Of course, as the saying goes: space rocks keep getting bigger. To represent larger celestial objects, we need to allow our adder to
add up to `6` digit numbers with a `6` digit output. But, if we can only represent `3` digits at a time, dare I need to ask _HOW_!?

Here, we will work with the concepts of _Most Significant_ and _Least Significant_ parts of numbers. Yes, some parts of numbers are 
apparently better than others. This is different than what you might know from scientific mathematics: _significant digits_. Here,
we need to develop a way to separate big numbers so that we can add them.

Complete this work in [src/high_dimension_adder.cardiac](src/high_dimension_adder.cardiac).

## Learning objectives

* describe and use elements of a simple, stored-program computer
* synthesize advanced uses of established opcodes (particularly `JMP`) to develop subroutine-based programs
* apply the `SFT` opcode to shift data
* develop a semi-autonomous "Nim" program to successfully mine piles of materials

## Reminder of the CARDIAC ISA

|Opcode|"Mnemonic"|Operation|
|:-----|:---------|:---------|
|`0`     |`INP`        |Read input card entry into memory |
|`1`     |`CLA`	       |Clear accumulator and add from memory (load) |
|`2`     |`ADD`        |Add from memory to ACC |
|`3`     |`TAC`        |Test ACC and jump if negative |
|`4`     |`SFT`        |Shifts ACC left by `l` digits and right by `r` digits where `4lr` (i.e. `413`) is the expression |
|`5`     |`OUT`        |Write memory location to output card |
|`6`     |`STO`        |Store ACC to memory |
|`7`     |`SUB`        |Subtract memory from ACC |
|`8`     |`JMP`        |Jump and save PC |
|`9`     |`HALT`       |Halt and reset |

## Instructions

To recieve credit for this assignment, write your program in the [nim.cardiac](src/nim.cardiac) file
in the `src` folder. Be sure to follow the convention of listing relevant `ADDRESS` register for the instruction,
followed by the `OPCODE` and target `ADDRESS` register for the command.

For example:

```
ADDRESS CONTENTS    COMMENTS
17      034         Read "A"
```

**Note**: You will work in pairs, but you should submit _your own assignment_ (including "training" programs)

### Working outside of class

To finish developing these programs, you may need to use the [CARDIAC simulator](https://www.cs.drexel.edu/~bls96/museum/cardsim.html).
This simulator works slightly differently than our physical CARDIAC unit, but follows the same opcodes, rules, and general principles.
Some notes about using the simulator:

* In the `CPU` section, the `PC` value simulates where you put the "bug" in the CARDIAC Memory Unit
* The buttons at the bottom allow you to `Run` or `Step` through the program; I suggest `Step` as it executes instruction-by-instruction
* `Reset` resets the current stored program; `Clear` erases programs stored in the `Memory` section
