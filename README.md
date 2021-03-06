# Linear-Logic-Proof-Assistant
Sequent calculus based proof assistant for ILL

Based off the following presentation of ILL: http://llwiki.ens-lyon.fr/mediawiki/index.php/Intuitionistic_linear_logic

The language of ILL is defined as follows (each binary operation is right-associative, from greatest-to-lowest precedence):

    formula ::=  P
                |0
                |1
                |Tr
                |!formula
                |formula * formula
                |formula & formula
                |formula + formula
                |formula -o formula

## Sequents

The general proof context is a list of sequents which keep track of everything that you still need to prove, along with your available assumptions.

Generally, a sequent looks like

    H1 : P1
    .
    .
    .
    Hn : Pn
    _______
    Q

In this example, `Q` is the goal to be proved and `P1,...,Pn` are the assumptions which are allowed to be used.  `H1,...,Hn` are the respective names assigned to these assumptions.

The order in which assumptions appear in a sequent never matter, but for the sake of convenience, tactics will be illustrated by assuming that all relevant assumptions appear together in a contiguous block.

## Proofs

To begin a proof of a formula , enter "Theorem" followed by that formula.  For instance, `Theorem P -o P` will generate the sequent with `P -o P` as a goal and no assumptions:

    _______
    P -o P

The goals and sequencts can be modified using tactics:

**`id`**

This will remove the top sequent when there is exactly one assumption which matches the goal:

    H : P

    _______

    P

is removed.

**`one`**

This will remove the top sequent when the goal is `1` and there are no assumptions:

    _______
    1
 
 is removed.
 
 **`true`**
 
This will remove the top sequent when the goal is  `Tr`:

     ...
     _______
     Tr
 
 is removed.
 
 **`zero`**
 
This will remove the top sequent when `0` can be found in the assumptions:

    ...
    H : 0
    _______
    P

is removed.

**`intro`**

When the goal is a `-o`, the antecedent is added as an assumption, making the consequent the new goal:

    ...
    _______
    P -o Q

is replaced by 

    ...
    H : P
    _______
    Q

**`remove H`**

This removes an assumption `H` which is either `1` or an `!`:

    ...
    H : 1 / !P
    _______
    Q

is replaced by

    ...
    _______
    Q

**`left`**

When the goal is a `+`, this changes the goal to the left disjunct:

    ...
    _______
    P + Q

is replaced by

    ...
    _______
    P

**`right`**

When the goal is a `+`, this changes the goal to the right disjunct:

    ...
    _______
    P + Q

is replaced by

    ...
    _______
    Q

**`promote`**

When the goal and all assumptions are `!`'s, this changes the goal by removing the `!`:

    H1 : !P1
    ...
    Hn : !Pn
    _______
    !Q

is replaced by

    H1 : !P1
    ...
    Hn : !Pn
    _______
    Q

**`split`**

When the goal is a `&`, this creates two proof branches, whose goals are the two conjuncts of the original goal, and whose assumptions are unchanged:

    ...
    _______
    P & Q

is replaced by both

    ...
    _______
    P

and 

    ...
    _______
    Q
    
**`split [H1 ... Hn]`**

When the goal is a `*`, this creates the two proof branches, whose goals are the two conjuncts of the original goal, where `H1,...,Hn` are made the assumptions of the first, and the remaining original assumptions are made the assumptions of the second:

    H1 : P1
    ...
    Hn : Pn
    Hn+1 : Pn+1
    ...
    Hn+k : Pn+k
    _______
    P * Q

is replaced by both

    H1 : P1
    ...
    Hn : Pn
    _______
    P

and 

    Hn+1 : Pn+1
    ...
    Hn+k : Pn+k
    _______
    Q

**`cut P [H1 ... Hn]`**

Creates two proof branches, the first of which has goal `P` and assumptions `H1,...,Hn`, and the second with the original goal, with the remaining original assumptions along with `P` as assumptions:

    H1 : P1
    ...
    Hn : Pn
    Hn+1 : Pn+1
    ...
    Hn+k : Pn+k
    _______
    Q

is replaced by both

    H1 : P1
    ...
    Hn : Pn
    _______
    P

and

    Hn+1 : Pn+1
    ...
    Hn+k : Pn+k
    H : P
    _______
    Q

**`left H`**

When `H` is a `&`, replaces `H` with the left conjunct:

    ...
    H : P & Q
    _______
    R

is replaced by

    ...
    H : P
    _______
    R

**`right H`**

When `H` is a `&`, replaces `H` with the right conjunct:

    ...
    H : P & Q
    _______
    R

is replaced by

    ...
    H : Q
    _______
    R

**`destruct H` (for `*`)**

When `H` is a `*`, replaces `H` with both conjuncts:

    ...
    H : P * Q
    _______
    R

is replaced by

    ...
    H1 : P
    H2 : Q
    _______
    R

**`destruct H` (for `+`)**

When `H` is a `+`, creates two proof branches where `H` is replaced by each disjunct:

    ...
    H : P + Q
    _______
    R

is replaced by

    ...
    H : P
    _______
    R

and

    ...
    H : Q
    _______
    R

**`apply H [H1 .. Hn]`**

When `H` is a `-o`, creates two proof branches, the first of which whose goal is the antecedent of `H` and with assumptions `H1,...,Hn`, and the second of which with the original goal, and with assumptions which are the remaining original assumptions along with the consequent of `H`:

    H1 : P1
    ...
    Hn : Pn
    Hn+1 : Pn+1
    ...
    Hn+k : Pn+k
    H : P -o Q
    _______
    R

is replaced by both

    H1 : P1
    ...
    Hn : Pn
    _______
    P

and

    Hn+1 : Pn+1
    ...
    Hn+k : Pn+k
    H : Q
    _______
    R

**`derelict H`**
When `H` is a `!`, removes the `!` from `H`:

    ...
    H : !P
    _______
    Q

is replaced by

    ...
    H : P
    _______
    Q

**`duplicate H`**
When `H` is a `!`, creates a new copy of `H`:

    ...
    H : !P
    _______
    Q

is replaced by

    ...
    H : !P
    H1 : !P
    _______
    Q
