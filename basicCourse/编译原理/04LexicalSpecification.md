# Lexical Specification

![lexicalSpecification](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-lexicalSpecification.JPG?raw=true)

#### Lexical Specification
| lexical specification |  |  |
| - | - | - |
| At lease one | A<sup>\+</sup> | AA<sup>\*</sup> |
| Union | A|B | A+B |
| Option | A? | A+ε |
| Range | 'a' + 'b' + … + 'z' | [a-z] |
| Excluded range | complement of [a-z] | [^a-z] | 

- Last lecture: a specification for the predicate s∈L(R)
- Not enough!
- **What to do?**
    1. write a rexp(regualr expression) for the lexemes of each token class
        + Number = digit<sup>\+</sup>
        + Keyword = 'if' + 'else' + ...
        + Identifier = letter(letter + digit)<sup>\*</sup>
        + OpenPar = '('
        + ...
    2. construct R, matching all lexemes for all tokens
        + R = Keyword + Identifier + Number + ... = R1 + R2 + R3 + ...
    3. let input be x1...xn
        + for i <= i <= n check x1...xi ∈ L(R)
    4. if success, then we know that x1..xi ∈ L(R<sub>j</sub>) for some j
    5. remove x1...xi from input and go to (3)
- **How much input is need?**
    + x1...xi ∈ L(R),
    + x1...xj ∈ L(R),
    + i ≠ j,
    + matches as long as possible. "Maximal much"
- **Which token is used?**
    + x1...xi ∈ L(R),
    + R=R1 + ... + Rn,
    + x1...xi ∈ L(R<sub>j</sub>),
    + x1...xi ∈ L(R<sub>k</sub>)
        * <small>Keyword='if' + 'else' + ...  
        Identifiers = letter(letter + digit)<sup>\*</sup>  
        if ∈ L(Keywords), if ∈ L(Identifiers)
        </small>
    + choose the one list first(highest priority match)
- **What to do if no rule matches?**
    + x1…xi ∉ L(R)
    + ERROR = [all strings not in the lexical specification], put it last in priority.
- **to summarise:**
    + Regular expression are a concise notation for string patterns
    + Use in lexical analysis requires small extensions
        * To resolve ambiguities(matches as long as possion, highest priority match)
        * To handle errors
    + Good algorithm known
        * Require only single pass over the input
        * Few operations per character(table lookup)
#### Finite Automata
- **Regular expression = specification**
- **Finite automata = impllementation**
- **A finite automaton consists of**
    + **An input alphabet: Σ**
    + **A finite set of states: S**
    + **A start state: n**
    + **A set of accepting state: F⊆S**
    + **A set of transitions: state→<sup>input</sup>state**
- transition s1→<sup>a</sup>s2 means
    + in state s1 on input a go to state s2
- if end of input and in accepting state => accept
- other => reject(terminates in state s ∉ F; gets stuck)
- Language of a finite automata ≡ set of accepted strings

![finiteAutomata](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-finiteAutomata.JPG?raw=true)

- **Deterministic Finite Automata(DFA)**
    + **One transition per input per state**
    + **No ε-moves**

![wrongDFA](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-wrongDFA.JPG?raw=true)

- **Nondeterministic Finite Automata(NFA)**
    + **Can have multiple transitions for one input in a given state**
    + **Can have ε-moves**
- A DFA takes only one path through the state graph per input, while a NFA can choose different path. A NFA accepts if some choices lead to an accepting state.

![DFAvsNFA](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-DFAvsNFA.JPG?raw=true)

- DFAs and NFAs recognize the same set of languages
    + regular languages
- DFAs are faster to execute
    + there are on choices to consider
- NFAs are, in general, small
    + exponentially small
#### Regular Expression to NFAs
![regularExpressionToNFAs](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-regularExpressionToNFAs.JPG?raw=true)
#### NFA to DFA
![NFAtoDFA](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-NFAtoDFA.JPG?raw=true)
#### Implementing Finite Automata
![implementingFiniteAutomata](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-implementingFiniteAutomata.JPG?raw=true)