# Logikon

Logikon is a logical/functional language for smart contracts featuring
simplicity and security.  Logical and functional languages such as Prolog and
Haskell inspire the design of Logikon, aiming at being elegant and simple.
Logikon also borrows the SMTLib2 language to write function inner expressions.

The code is not sequential, and is written as a set of logical constraints.
Ideally these logical constraints are statically and formally verified against
a specification given in the code in compile-time. Logikon compiles to YUL, the
intermediate language used in Solidity, which compiles to multiple EVM bytecode
versions.

# Compiler

https://github.com/logikon-lang/logikon

# Grammar

https://github.com/logikon-lang/logikon/blob/master/src/logikon.pest

# Examples

```
define sum (Uint Uint) -> Uint
case (X Y) Z :-
    (= Z (+ X Y)).
```

```
define double (Uint) -> Uint
case (X) D :-
    (= D (sum X X)).
```

```
define triple (Uint) -> Uint
case (X) T :-
    (= D (ssum X X))
    (= T (ssum X D)).
```

```
define recursive min (List) -> Uint
case ([X]) X.
case ([H:T*]) X :-
    (= Y (min T))
    (= X (ite (< H Y) H Y)).
```

```
declare Balance Array.
declare Owner Uint.

define isOwner (Addr) _ :- (= Addr Owner).

define transfer (Uint Uint) -> Bool
case (To Amount) _ :-
    (= FromBalance (select Balance Sender))
    (>= FromBalance Amount)
    (= ToBalance (select Balance To))
    (= Balance2 (store Balance Sender (- FromBalance Amount)))
    (= Balance3 (store Balance2 To (+ ToBalance Amount)))
    (prove (= (sum Balance3) (sum Balance)))
    (update Balance Balance3).
```
