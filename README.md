# CMPSC 200: Single-Pile Nim

| Date              |          |
|:------------------|:---------|
| 5 September 2023 | Assigned  |
| 12 September 2023 | Due, start of class       |
| Status           | [![GatorGrader](../../actions/workflows/main.yml/badge.svg)](../../actions/workflows/main.yml) |


## Learning objectives

* describe and use elements of a simple, stored-program computer
* synthesize advanced uses of established opcodes (particularly `JMP`) to develop subroutine-based programs
* apply the `SFT` opcode to shift data
* develop a semi-autonomous "Nim" program to successfully mine piles of materials

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

### Lab: Single-pile Nim

Complete this work in [src/nim.cardiac](src/nim.cardiac).

You know that once someone beats you to a minable destination, they have all the gold, er, space rocks. We have the ability to send
a mining robot to some nearby asteroids to compete with others already there. We know we can't gather all the rocks, but we're going
to do our best to mine _more_ rocks than the competition.

#### Competing in `nim`

Perhaps the simplest way to understand this activity is to first do it in the analog world. You will be given
`13` paperclips with which to play a human opponent, except you're taking the role of competing mining robots.

#### Rules

* intergalactic trade treaties indicate that robots can only take `1`, `2`, or `3` rocks at a time; no more, no less
* robots _cannot mimic each other_ meaning that:
  * if a robot just took 2 rocks, the other robot can only take `1` or `3` rocks, et al.
* robots "lose" the pile collection contest (i.e. the game ends) if:
  * there are no more rocks left in the common pile to take (mathematical loss)
  * there is only `1` rock left and the opposing robot has just taken `1` rock (no mimcry)

#### Programming `nim`

Your program should:

* read the number of rocks taken by our mining robot
* keep track of the pile's residue: that is, the number of rocks that would be left in the pile if the highest possible multiple of four were subtracted from it
* keep track of the actual number of rocks in the pile; print that number after every move (player and machine)
* print the number of rocks CARDIAC takes

##### Suggestion

When programming the autonomous `nim` agent, it's likely that you'll want to keep track of the following quanitites:

|Shorthand |Definition |Notes |
|:---------|:----------|:-----|
|N  |Number of pebbles in pile | Currnet number of objects in pile at any given time |
|P  |The number of pebbles the opposing entity takes |
|C  |The number of rocks that our CARDIAC takes      |
|R  |Number of rocks which would remain after a sequence of moves<sup>✝</sup> |

<sup>✝</sup> This is less obvious than it seems at first glance. Here's a hint:

We need to be able to reduce the CARDIAC's choice to a potential range of moves of between 1 and 3. To this
point, we know a few things:

* the `1` (`CLA`) opcode always resets the ACC to `000` before adding whatever the instruction dictates (i.e. `123`)
* the range of moves 1-3 _really_ starts counting from `0`, which is an illegal move (see [the rules](#rules))
  * this indicates that we need to add or subtract `4` some number of times to meaningfully arrive at a _legal_ move
* one way to solve this implements the `SFT` opcode in a clever way

This also builds an instruction similar to the way that we built up the magical `8--` instruction from Memory Cell 99 and combines some techniques from our higher-dimensional adder. Consider the following routine which _builds_ an instruction:

```
ADDRESS  CONTENTS    COMMENTS
10       820         JMP 20
11       500         Print the bootstrapped number
20       199         Copy the JMP point
21       622         Store the JMP point as next instruction
22                   Reserve for JMP
```

In this case, we'll build an instruction with a different opcode corresponding the bootstrapped set of data words below. What opcode would both target a memory cell and load it in the ACC?

#### Bootstrapped (pre-loaded) data

Our space center has a sophisticatefd lab setup and can simulate conditions and conflicts between ourselves and other robots. Here's a set of data that describes the best-case scenario of moves:

```
ADDRESS CONTENTS  COMMENTS
                  TABLE OF PREDEFINED CARDIC MOVES
00 	    001
01 	    001
02 	    002
03 	    003
10 	    003
11    	002
12 	    002
13 	    003
20 	    001
21	    001
22 	    001
23 	    003
30 	    001
31 	    001
32 	    002
33 	    002
```

You will need to "bootstrap" the above table into your CARDIAC unit to simulate the moves that other robots might make. These are all _data words_, meaning that they aren't executed, only read.

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

### Assignment "Hacks"

See the `Suggestion` below to challenge yourself to implement a Hack. As always, you are allowed to develop
your own Hack to satisfy this stretch goal. Place the code for the Hack inline with the code you write to 
create the liftoff sequence program (i.e. make it part of the program).

In order to recieve credit for the Hack, you must fill out the [hack.md](docs/hack.md) file located in the
`docs` folder.

#### Suggestions

Implement whichever "hack" you intend to do in the file named in the suggestion.

##### `decoder.cardiac`

Implement the dynamic JMP for the `decoder.cardiac` program as many times as is optimal (minimum 1). Does
this add any value to the program? Why or why not?

##### `subroutine.cardiac`

Add subroutines to add, subtract, and multiply `n` numbers appearing in a sequence terminated by `-1`.

##### `high_dimension_adder.cardiac`

###### Option 1

Is it possible to implement high-dimension _subtraction_ on the CARDIAC? How does the approach
differ from addition, if at all?  To claim credit for this hack, you must program it alongside the 
adder rather than replacing it. This means that you need to add a way to tell the program to 
_choose_ addition or subtraction "modes".

###### Option 2

How far can we push the precision of our adder? Is it possible to add `9`-digit numbers? `12`-digit numbers?

#### Make it your own

You are free to develop your own "Hack" for this assignment, provided that it elaborates on the liftoff
countdown counter we're developing this week.

### Working outside of class

To finish developing these programs, you may need to use the [CARDIAC simulator](https://www.cs.drexel.edu/~bls96/museum/cardsim.html).
This simulator works slightly differently than our physical CARDIAC unit, but follows the same opcodes, rules, and general principles.
Some notes about using the simulator:

* In the `CPU` section, the `PC` value simulates where you put the "bug" in the CARDIAC Memory Unit
* The buttons at the bottom allow you to `Run` or `Step` through the program; I suggest `Step` as it executes instruction-by-instruction
* `Reset` resets the current stored program; `Clear` erases programs stored in the `Memory` section
