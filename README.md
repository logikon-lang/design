# Logikon

Logikon is a logical/functional language for smart contracts.
Assertions are statically checked and are not compiled into EVM bytecode.
The code is not sequential, it's a conjunction of logical constraints.

Examples:

```
define sum (Uint Uint) -> (Uint)
case (1 1) (2).
case (X Y) (Z) :-
    (= Z (+ X Y)).
```

```
define double (Uint) -> (Uint)
case (X) (D) :-
    (= D (sum X X)).
```

```
define triple (Uint) -> (Uint)
case (X) (T) :-
    (= D (ssum X X))
    (= T (ssum X D)).
```

```
define recursive min (List) -> (Uint)
case ([X]) (X).
case ([H:T*]) (X) :-
    (= Y (min T))
    (= X (ite (< H Y) H Y)).
```

```
declare Balance public Array.
declare Owner UInt.

define isOwner (Addr) :- (= Addr Owner).

define transfer (Uint Uint) -> (Bool)
case (To Amount) (_) :-
    (= FromBalance (select Balance Sender))
    (>= FromBalance Amount)
    (= ToBalance (select Balance To))
    (= Balance2 (store Balance Sender (- FromBalance Amount)))
    (= Balance3 (store Balance2 To (+ ToBalance Amount)))
    (prove (= (sum Balance3) (sum Balance)))
    (update Balance Balance3).
```

Grammar:
https://github.com/logikon-lang/logikon_rust/blob/master/src/logikon.pest
