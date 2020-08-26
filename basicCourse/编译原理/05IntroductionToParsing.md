# Introduction to Parsing
#### Introduction to Parsing
- Regular Languages
    + The **weakest** formal languages widely used
    + Many applications
- Consider the language:{(<sup>i</sup>)<sup>i</sup> | i>=0}
<small>
()  
(())  
((()))  
...  
((1+2)\*3)  
if then  
&nbsp;&nbsp;if then  
&nbsp;&nbsp;&nbsp;&nbsp;if then  
&nbsp;&nbsp;&nbsp;&nbsp;fi  
&nbsp;&nbsp;fi  
fi
</small>

![regularLanguages](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-regularLanguages.JPG?raw=true)

| Phase | Input | Output |
|- | - | - |
| Lexer | String of characters | String of tokens |
| Parser | String of tokens | Parse tree(May be implicit) | 

#### Context-Free Grammars(CFG)
- Not all strings of tokens are programs
- parser must distinguish between valid and invalid strings of tokens
- We need
    + a language for describing valid strings of tokens
    + A method for distinguishing valid form invalid string of tokens
- **Programming languages have recursive structure**
- **Context-free grammars are a natural notation for this recursive structure**
- **A Context-free grammar(CFG) consist of** 
    + **A set of terminals: T**
    + **A set of non-terminals: N**
    + **A start symbol: S(S∈N)**
    + **A set of productions: X→Y1…Yn (X∈N, Yi ∈ N U Y U {ε})**

1. **Begin with a string with only the start symbol S**
2. **Replace any non-terminal X in the string by the right-hand side of some production X→Y1…Yn**
3. **Repeat (2) until there are no non-terminals**

- α<sub>0</sub>→<sup>\*</sup>α<sub>n</sub>(in >=0 steps) means α<sub>0</sub>→α<sub>1</sub>→α<sub>2</sub>→...→α<sub>n</sub>
- Let G be a context-free grammar with start symbol S. then the language L(G) of G is:
    + {a<sub>i</sub>...a<sub>n</sub> | ∀<sub>i</sub>a<sub>i</sub>∈T^S→<sup>\*</sup>a<sub>i</sub>...a<sub>n</sub>}
- Terminals are so-called because there are no rules for replacing them, once generated, terminals are permanent.
- Terminals ought to be tokens of the language.

<small>
EXPR → if EXPR then EXPR else EXPR fi | while EXPR loop EXPR pool | id  
&nbsp;&nbsp;id  
&nbsp;&nbsp;if id then id else id fi  
&nbsp;&nbsp;while id loop id pool  
&nbsp;&nbsp;if while id loop id pool then id else id fi  
&nbsp;&nbsp;if if id then id else id fi then id else id fi  
&nbsp;&nbsp;...   
E → E + E | E * E | (E) | id  
&nbsp;&nbsp;id 
&nbsp;&nbsp;id + id  
&nbsp;&nbsp;id \* id  
&nbsp;&nbsp;(id + id) \* id   
&nbsp;&nbsp;...
</small>

- The idea of a CFG is a big step, but
    + Menbership in a language is "yes" or "no"; also need parse tree of the input.
    + Must handle errors gracefully.
    + Need an implementation of CFG's.(e.g.,bison).
- From of the grammar is important
    + Many grammars generate the same language
    + Tools are sensitive to the grammar
#### Derivations
- A derivation is a sequence of productions.<small>S→…→…→…→…→…</small>
- A derivation can be drawn as a tree
    + start symbol si the tree's root
    + for a production, X→Y1…Yn add children Y1…Yn to node X

![derivation](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-derivation.JPG?raw=true)

- A parse tree has
    + Terminals at the leaves
    + Non-terminals at the interior nodes
- An in-order traveral of the leaves is the original input
- The parse tree shows the association of operations, the input string does not
- left-most derivation: at each step, replace the left-most non-terminal
- right-most derivation: at each step, replace the right-most non-terminal
- left-most and right-most derivation have the same parse tree
- **to summarize:**
    + **We are not just interested in where s∈L(G), we need a parse tree for s**
    + **A derivation defines a parse tree, but one parse tree may have many derivations**
    + **Left-most and right-most derivation are important in parser implementation**
#### Ambiguity
![ambiguity](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-ambiguity.JPG?raw=true)

- **A grammar is ambiguous if it has more than one parse tree for some string. equivalently, there is more than one right-most or left-most derivation for some string.**
- Ambiguity is bad, leaves meaning of some programs ill-defined.
- **There are several way to handle ambiguity.**
- **Most direct method is to rewrite grammar unambiguously.**
- Enforces precedence of \* over \+

<small>
E → E' + E | E'  
E' → id \* E' | id | (E) \* E' | (E)  
&nbsp;&nbsp;E → E'+E → E'+E'+E → E'+E'+E'+E → E'+E'+E'+E'+E →…→ E'+…+E'  
&nbsp;&nbsp;E' → id\*E' → id\*id\*E' → id\*id\*id\*E' → id\*id\*id\*id\*E' → id\*…\*id  
&nbsp;&nbsp;E' → (E)\*E' → (E)\*(E)\*E' → (E)\*(E)\*(E)\*E' → (E)\*(E)\*(E)\*(E)\*E' → (E)\*…\*(E)
</small>

![ambiguity2](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-ambiguity2.JPG?raw=true)

- Impossible to convert automatically an ambiguous grammar to an unambiguous one.
- Used with care, ambiguity can simplify the grammar
    + sometimes, allows more natural definitions
    + we need disambiguation mechanisms.
- **Instead of rewriting the grammar, use the more natural(ambiguous) grammar, along with disambiguating declarations.**
- **Most tools allow precedence and associativity declarations to disambiguate grammars.**

![ambiguity3](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-ambiguity3.JPG?raw=true)
