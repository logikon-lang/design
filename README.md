# Logikon

Logikon is a logical/functional language for smart contracts.
Assertions are statically checked and are not compiled into EVM bytecode.
The code is not sequential, it's a conjunction of logical constraints.

Examples:

```
define ssum (X Y) Z :-
    (= Z (+ X Y)).
```

```
define double (X) D :-
    (= D (ssum X X)).
```

```
define triple (X) T :-
    (= D (ssum X X)),
    (= T (ssum X D)).
```

```
define min
([X]) X.
([H|T]) X :-
    (= Y min(T)),
    (= X (ite (< H Y) H Y)).
```

```
declare Balance public Array.
declare Owner UInt.

define isOwner (Addr) :- (= Addr Owner).

define transfer public (To Amount) :-
    (= FromBalance (select Balance Sender)),
    (>= FromBalance Amount),
    (= ToBalance (select Balance To)),
    (= Balance2 (store Balance Sender (- FromBalance Amount))),
    (= Balance3 (store Balance2 To (+ ToBalance Amount))),
    (prove (= (sum Balance3) (sum Balance))),
    (update Balance Balance3).
```

Grammar:

Contract := ElemList
ElemList := StateVar | Function | ElemList
StateVar := `declare` Identifier Visibility Type `.`
Visibility := Public | NULL
Type := UInt | Array | List
Function := `define` Identifier Visibility Cases
Cases := Case | Cases
Case := `(` ParamList `)` Return CaseBody `.`
Return := Parameter | NULL
ArrayIdentifier := `[` ArrayContent `]`
ArrayContent := NULL | Identifier | (Identifier `|` Identifier)
CaseBody := `:-` StatementList
ParamList := Parameter | ParamList
StatementList := Statement | (Statement `,` StatementList)
Statement := `(` (UserPred ArgsList) | UnaryOp Arg | BinaryOp Arg Arg | TernaryOp Arg Arg Arg) `)`
Arg := Identifier | Number | Statement
ArgsList := NULL | Arg ArgsList
UnaryOp := `prove` | `sum` | `not`
BinaryOp := `=`, `!=`, `<`, `>`, `<=`, `>=`, `+`, `-`, `*`, `/`, `update`, `select`
TernaryOp := `store`, `ite`
