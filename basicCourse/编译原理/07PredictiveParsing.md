# Predictive Parsing
#### Predictive Parsing
- like recursive-descent but parser can "predict" which production to use
    + by looking at the next few tokens
    + no backtracking
- **Predictive parsers accepts LL(k) grammars**
    + **L: left-to-right**
    + **L: left-most derivation**
    + **k: k tokens lookahead(k=1)**
- **In recursive descent,**
    + **at each step, many choices of production to use**
    + **backtracking used to undo bad choices**
- **In LL(1), at each step, only one choice of production**

<small>
Recall the grammar  
&nbsp;&nbsp;&nbsp;&nbsp; E → T + E | T  
&nbsp;&nbsp;&nbsp;&nbsp; T → int | int * T | (E)  
Hard to predict because  
&nbsp;&nbsp;&nbsp;&nbsp; For T two productions start with int  
&nbsp;&nbsp;&nbsp;&nbsp; For E it is not clear how to predict  
We need to left-factor the grammar  
&nbsp;&nbsp;&nbsp;&nbsp; E → T X  
&nbsp;&nbsp;&nbsp;&nbsp; X → + E | ε  
&nbsp;&nbsp;&nbsp;&nbsp; T → int Y | (E)  
&nbsp;&nbsp;&nbsp;&nbsp; Y → * T | ε  
The LL(1) parsing table:  

|  | int | \* | \+ | ( | ) | $ |
| - | - | - | - | - | - | - | 
| E | E→T X |  |  | E→T X |  |  |
| X |  |  | X→+ E |  | X→ε | X→ε |
| T | T→int Y |  |  | T→ (E) |  |  |
| Y |  | Y→<sup>\*</sup>T | Y→ε |  |Y→ε  | Y→ε |

Consider the [E,int] entry  
&nbsp;&nbsp;&nbsp;&nbsp;When current non-terminal is E and next input is int, use production E→T X  
Consider the [Y,\+] entry  
&nbsp;&nbsp;&nbsp;&nbsp;When current non-terminal is Y and next input is \+, use production Y→ε  
&nbsp;&nbsp;&nbsp;&nbsp;Y can be followed by \+ only is  Y→ε  
Consider the [E,\*] entry  
&nbsp;&nbsp;&nbsp;&nbsp;There is no way to derive a string starting with \* form non-terminal E
</small>

- Method similar to recursive descent, except
    + For the leftmost non-terminal S, we look at the next input token a and choose the production shown at [S,a]
- A stack records frontier of parse tree
    + Non-terminals that have yet to be expanded
    + Terminals that have yet to matched against the input
    + Top of stack = leftmost pending terminal or non-terminal
- Reject on reaching error state
- Accept on end of input & empty stack

![predictiveParsing](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-predictiveParsing.JPG?raw=true)

#### First Sets
- Consider non-terminal A, production A→α, token t, T[A,t] = α in two cases:
    + if α →<sup>\*</sup> tβ
        * α can derive at in the first position
        * we say that t ∈ First(α)
    + if A → α and α → <sup>\*</sup> ε and S →<sup>\*</sup> βAtδ
        * useful is stack has A, input is t, and A cannot derive t
        * in this case only option is to get rid of A(by deriving ε)
            - can work only if t can follow A in at least one derivation
        * we say that t ∈ FOLLOW(A)
- definition
    + First(X) = {t | X →<sup>\*</sup> tα} U {ε | X →<sup>\*</sup> ε}
- Algorithm sketch:
    1. First(t) = t(t is a terminal)
    2. ε∈First(X)  
        if X→ε  
        if X→A1…An and ε∈First(Aj) for 1<=i<=n  
    3. First(α) ⊆ First(X) if X→A1…Anα  
        and ε∈First(Ai) for 1<=i<=n

<small>
Recall the grammar:
E → T X  
X → + E | ε   
T → int Y | (E)  
Y → * T | ε  
First(E) = {int, (}  
First(X) = {+, ε}  
First(T) = {int, (}  
First(Y) = {*, ε}
</small>

#### Follow Sets
- Definition:
    + Follow(X) = { t | S →<sup>\*</sup> βXtδ} 
- Intuition
    + If X→A B then First(B) ⊆ Follow(A) and Follow(X) ⊆ Follow(B)  
        if B→^∗ ε then Follow(X) ⊆ Follow(A)  
    + If S is the start symbol then $ ⊆ Follow(S)
- Algorithm sketch:
    1. $ ∈ Follow(S)
    2. First(β) - {ε} ⊆ Follow(X) for each production A → αXβ
    3. Follow(A) ⊆ Follow(X) for each production A → αXβ where ε ∈ First(β)

<small>
Recall the grammar
E → T X  
X → + E | ε  
T → int Y | (E)  
Y → * T | ε  
Follow(E) = {$, )}, Follow(X)⊆Follow(E), Follow(E)⊆Follow(X),  
Follow(X) = {$, )},  
Follow(T) = {+, $, )}, Follow(E)⊆Follow(T),  
Follow(Y) = {+, $, )}, Follow(Y)⊆Follow(T), Follow(T)⊆Follow(Y),  
Follow('(') = {(, int},  
Follow(')') = {+, $, )},  
Follow('+') = {(, int},  
Follow('*') = {(, int},  
Follow(int) = {*, +, $, )}   
</small>

#### LL(1) Parsing Tables
- Consider a parsing table T for CFG(Context-Free Grammars)G
- For each production A→ α in G do:
    + For each terminal t ∈ First(α) do T[A,t] = α
    + If ε ∈ First(α), for each t ∈ Follow(A) do T[A,t] = α
    + If ε ∈ First(α) and $ ∈ Follow(A) do T[A, $] = α

<small>
Recall the grammar  
E → T X  
X → + E | ε  
T → int Y | (E)  
Y → * T | ε  

|  | int | \* | \+ | ( | ) | $ |
| - | - | - | - | - | - | - | 
| E | E→T X |  |  | E→T X |  |  |
| X |  |  | X→+ E |  | X→ε | X→ε |
| T | T→int Y |  |  | T→ (E) |  |  |
| Y |  | Y→<sup>\*</sup>T | Y→ε |  |Y→ε  | Y→ε |

Bad example: S → Sa | b  

|  | a | b | $ |
| - | - | - | - |
| S |  | S→b, S→Sa(multiply defined!) |  |

</small>

- If any entry is multiplly defined then G is not LL(1)
    + not left factored
    + left recursive
    + ambiguous
    + other grammars are not LL(1)
- Most programming language CFGs are not LL(1)
#### Bottom-up Parsing
- Bottom-up parsing is more general than (deterministic) top-down parsing
    + and just as efficient
    + builds on ideas in top-down parsing
- Bottom-up is the preferred method
- BOttom-up parsers don't need left-factored grammars

<small>
Revert to the "natural" grammar for our example:  
E → T + E | T  
T → int * T | int | (E)  
Consider the string: int * int + int  
Bottom-up parsing reduces a string to the start symbol by inverting productions.  

| int \* int \+ int | T → int |
| - | - |
|int \* T \+ int | T → int \* T|
| T \+ int | T → int |
| T \+ T | E → T |
| T \+ E | E → T \+ E| 
| E |  |

</small>

- **Important Fact 1 about bottom-up parsing:**
    + **A bottom-up parser traces a rightmost derivation in reverse.**
#### Shift-Reduce Parsing
- Important Fact 1 has an interesting consequence:
    + Let αβω be a step of a bottom-up parse
    + Assume the next reduction is by X → β
    + Then ω is a string of terminals
    + Why? Because αXω → αβω is a step in a right-most derivation.
- Idea: Split string into two substrings
    + Right: substring is as yet unexamined by parsing
    + Left substring has terminals and non-terminals
    + The dividing point is marked by a <u>|</u>
        * e.g. <u>αX|ω</u>
- Bottom-up parsing uses only two kinds of actions: Shift & Reduce
- Shift: Move <u>|</u> one place to the right
    + Shifts a terminal to the left string
        * <u>ABC|xyz => ABCx|yz</u>
- Apply an inverse production at the right end of the left string
    + <u>If A → xy is a production then Cbxy|ijk => CbA|ijk</u>

<small>
example:  
E → T + E | T   
T → int * T | int | (E)  
Consider the string: int * int + int     

| |int\*int\+int | shift |
| - | - | 
| int\|\*int\+int | shift |
| int\*|int\+int | shift | 
| int\*int|\+int | reduce T→int | 
| int\*T|\+int | reduce T → int \* T | 
| T|\+int | shift | 
| T\+|int | shift |
| T\+int| | reduce T → int | 
| T\+T| | reduce E → T | 
| T\+E| | reduce E → T \+ E | 
| E| |  |

</small>

- Left string can be implemented by a stack
    + Top of the stack is the |
- Shift pushes a terminal on the stack
- Reduce:
    + pops symbols off of the stack(production rhs(right hand side))
    + pushes a non-terminal on the stack(production lhs)
- In a given state, more than one action(shift or reduce) may lead to a valid parse
- If it is legal to shift or reduce, there is a shift-reduce conflict. not good
- If it is legal to reduce by two different productions, there is a reduce-reduce conflict. very bad


