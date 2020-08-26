# Introduction
#### Compilers vs. Interpreters
![compilersVSinterpreters](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-compilersVSinterpreters.JPG?raw=true)
#### structure of a compiler
1. lexical analysis
2. parsing
3. semantic analysis
4. optimization
5. code generation

- how to understand?
    + First step: recognize words.————smallest unit above letters.
        * *This is a sentence.*(recognize four words, blank, punctuation, capitalization)
        * *ist his ase nten nce.*(is this a sentence)
        * the goal of lexical analysis is to divide program text into "words" or "tokens".
        * *if x == y then z = 1; else z = 2;*
    + Second step: understand sentence structure————paring(Diagramming Sentences)  
    ![sentenceStructure](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-sentenceStructure.JPG?raw=true)
    + Third step: understand "meaning".
        * compilers perform **limited** semantic analysis to catch inconsistencies.
            - *Jack said Jerry left his assignment at home.*(his: Jack or Jerry)
            - *Jack said Jack left his assignment at home.*(his: Jack or Jack or ？)
        * Programming languages define strict rules to avoid such ambiguities.
```
{
    int Jack = 3;
    {
        int Jack = 4;
        cout << Jack;
    }
}
```
        * compilers perform many semantic checks besides variable binding.
            - *Jack left her homework at home.*(Jack:male; her:female. A "type mismatch" between her and Jack)
    + Fourth step: optimization
        * optimization has no strong counterpart in English.
            - *But ~~a litter bit~~ like editing*
            - &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  akin to 
        * automatically modify programs so that they run faster and use less memory and ...
            - *X = Y * 0 is the same sa X = 0*(This optimization is not right! This rule is valid for integers, but not valid for float points because NAN * 0 = NAN)
    + Fifth step: Code generation. produce assemble code(usually).
        * generally: a translation into another language. anlogous to human translation.
    + The overall structure of almost every compiler adheres to out outline(follow the rules)
    + The proportions have changed since FORTRAN.
![compilersStructure](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-compilerStructure.JPG?raw=true)
#### The Economy of Programming Languages
- how programming languages work in real world?
- why certain languages are used and others are not?
- why are there so many programming languages?
    + application domains have distinctive/conflicting needs.

| application domain | needs | needs | needs | needs | languages |
| ---- | ---- | ---- | ---- | ---- | ---- | 
| scientific computing | good float point | good arrays | parallelism | ... | FORTRAM |
| business applications | persistence | report generation | data analysis | ... | SQL |
| systems programming | control of resources | real time constraints | ... | ... | C/C++ |

- why are there new programming languages(sinces we already have so much)？
    + Claim: programmer training is the dominant cost for a programming language.
        * =>1. widely-used languages are slow to change.
        * =>2. easy to start a new language.(0 user, 0 programmer training cost)  
        productivity > training cost
        * =>3. languages adopted to fill a void.
        * => new languages tend to look like old language.(java vs. C++)
- what is a good programming language?
    + there is no universally accepted metric for language design.