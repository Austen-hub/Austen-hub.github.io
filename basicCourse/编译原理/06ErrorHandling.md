# Error Handling
#### Error Handling
- Purpose of the compiler is 
    + to detect non-valid programs
    + to dranslate the valid ones
- Many kinds of possible error(e.g. in C)

| error kind | example | detected by |
| - | - | - | 
| lexical | ...$... | lexer |
| Syntax | ...x\*%*.... | Parser |
| Semantic | ...int x; y=x(3);... | Type checker |
| Correctness | your favorite program | Tester/User |

- Error handler should
    + report errors accurately and clearly
    + recover from an error quickly
    + not slow down compliation of valid code

1. **Panic mode(used today)**
    - Panic mode is simplest, most popular method
    - When an error is detected:
        + Discard tokens until one with a clear role is found
        + Continue from there
    - Looking for synchronizing tokens
        + Typically the statement or expression terminators

<small>
Consider the erroneous expression (1++2) + 3  
Panic-mode recovery: skip ahead to next integer and then continue  
Bison: use the special terminal error to describe how much input to skip  
&nbsp;&nbsp;E → int | E + E | (E) | error int | (error)
</small>

2. **Error productions(used today)**
    + specify known common mistakes in the grammar

<small>
example: write 5 x instead of 5\*x  
solution: add the production E → … | EE.
disadvantage: complicates the grammar
</small>

3. **Automatic local or global correction(used in the past)**
    - idea: find a correct "nearby" program
        + try token insertions and deletions
        + exhaustive search
    - disadvantage:
        + hard to implement
        + slow down parsing of correct programs
        + "nearby" is not necessarily "the intended" program

- past
    + slow recompilatioin cycle(even one a day)
    + find as many errors in one cycle as possible
- present
    + quick recompilation cycle
    + users tend to correct one error/cycle
    + comlex error recovery is less compelling
#### Abstract Syntax Trees
- A parser traces the derivation of a sequence of tokens, but the rest of the compiler needs a structural representation of the program.
- Abstract syntax trees(AST) like parse trees but ignore some details

![abstractSyntaxTree](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-abstractSyntaxTree.JPG?raw=true)

#### Recursive Descent Parsing
- The parse tree is constructed
    + from the top
    + from left to right
- Terminals are seen in order of appearance in the token stream.

![recursiveDescentParsing](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-recursiveDescentParsing.JPG?raw=true)

#### Recursive Descent Algorithm
- Let TOKEN be the type of tokens
    + Special tokens INT, OPEN, CLOSE, PLUS, TIMES
- Let the global <u>next</u> point to the next input token
- Define boolean functions that check for a match of:
    + A give token terminal
        * <u>bool term(TOKEN tok){return \*next\+\+ == tok;}</u>
    + The nth production of S:
        * <u>bool S<sub>n</sub>(){...}</u>
    + Try all productions of S:
        * <u>bool S(){...}</u>

<small>
consider the grammar  
&nbsp;&nbsp;&nbsp;&nbsp;E → T | T + E  
&nbsp;&nbsp;&nbsp;&nbsp;T → int | int * T | (E)  
for production,E → T  
&nbsp;&nbsp;&nbsp;&nbsp;bool E1(){return T();}  
for production,E → T + E  
&nbsp;&nbsp;&nbsp;&nbsp;bool E2(){return T() && term(PLUS) && E();}  
for all productions of E(with backtracking)  
&nbsp;&nbsp;&nbsp;&nbsp;bool E() {  
&nbsp;&nbsp;&nbsp;&nbsp;TOKEN \*save = next;  
&nbsp;&nbsp;&nbsp;&nbsp;return (next = save, E1()) ||  
&nbsp;&nbsp;&nbsp;&nbsp;(next = save,E2());}  
functions for non-terminal T  
&nbsp;&nbsp;&nbsp;&nbsp;bool T1() {return term(INT);}  
&nbsp;&nbsp;&nbsp;&nbsp;bool T2() {return term(INT) && term(TIMES) && T();}  
&nbsp;&nbsp;&nbsp;&nbsp;bool T3() {return term(OPEN) &&E() && term(CLOSE);}  
&nbsp;&nbsp;&nbsp;&nbsp;bool T(){  
&nbsp;&nbsp;&nbsp;&nbsp;TOKEN \*save = next;  
&nbsp;&nbsp;&nbsp;&nbsp;return (next = save, T1())  
&nbsp;&nbsp;&nbsp;&nbsp;|| (next = save, T2())  
&nbsp;&nbsp;&nbsp;&nbsp;|| (next = save, T3()));}
</small>

- to start the parser
    + initialize <u>next</u> to point to first token
    + invoke E()

![recursiveDescentParsing2](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-recursiveDescentParsing2.JPG?raw=true)

#### Recursive Descent Algorithm Limitation
![recursiveDescentAlgorithmLimitation](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-recursiveDescentAlgorithmLimitation.JPG?raw=true)

- if a production for non-terminal X succeeds, cannot backtrack to try a different production for X later.
- General recursive-descent algorithms support such "full" backtracking.
    + can implement any grammar
- Presented recursive descent algorithm is not general.
    + but is easy to implement by hand
- Sufficient for grammars where for any non-terminal at most one production can succeed.
- THe example grammar can be rewritten to work with the presented algorithm.
    + by left factoring, the topic of a future video.
#### Left Recursion
<small>
Consider a production S→Sa
&nbsp;&nbsp;&nbsp;&nbsp;bool S1() {return S() && term(a);}  
&nbsp;&nbsp;&nbsp;&nbsp;bool S() {return S1();}  
S() goes into an infinite loop
</small>

- **A left-recursive grammar has a non-terminal S**
    + **S→<sup>\+</sup> Sα for some α**
- **Recursive descent does not work in such cases.**

<small>
Consider the left-recursive grammar  
&nbsp;&nbsp;&nbsp;&nbsp;S → S α | β  
S generates all strings starting with a β and followed by any number of α's  
&nbsp;&nbsp;&nbsp;&nbsp;S → Sα → Sαα → Sααα → … → Sα…α → βα…α  
can rewrite using right-recurision  
&nbsp;&nbsp;&nbsp;&nbsp;S → βS'  
&nbsp;&nbsp;&nbsp;&nbsp;S' → αS' | ε  
&nbsp;&nbsp;&nbsp;&nbsp;S → βS' → βαS'→βααS'→βαααS'→…→βα…αS'→βα…α
</small>

- **in general**
    + **S→S α<sub>1</sub>  |…|  α_<sub>n</sub> |  β<sub>1</sub> |…| β<sub>m</sub>**
- **All strings derived from S start with one of β<sub>1</sub>, ..., β<sub>m</sub>**
- **and continue with several instances of α<sub>1</sub>,..., α_<sub>n</sub>**
- **Rewrite as**
    + S → β<sub>1</sub>S' |...| β<sub>m</sub>S'
    + S'→ α<sub>1</sub>S' |...| α<sub>n</sub>S' | ε

<small>
The grammar
&nbsp;&nbsp;&nbsp;&nbsp; S → Aα | δ
&nbsp;&nbsp;&nbsp;&nbsp; A →Sβ
is also left-recusive bacause S →<sup>\+</sup> Sβα
</small>

- This left-recursion can also be elminated, see Dragon Book for general algorithm
- **To summarize:** 
    + Recursive descent
        * Simple and general parsing strategy
        * Left-recursion must be eliminated first
        * ..but that can be done automatically
    + Used in prodution compilers
        * e.g.,gcc