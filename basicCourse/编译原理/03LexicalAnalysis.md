# Lexical Analysis
#### Lexical analysis
- given:
    + <small>\tif (i == j)\n\t\tz = 0;\n\telse\n\t\tz = 1;</small>
- want:
    + if (i == j)  z = 0;  else  z = 1;
- Token Class(or Class)
    + in English:
        * Noun, verb, adj,...
    + in a programming language:
        * Identifier: strings of letter or digits, start with a letter
        * Integer: a non-empty string of digits
        * Keyword: "else" or "if" or "begin" or ...
        * Whitespace: a non-empty sequence of blanks, newlines, and tab
        * ...
        
![tokenClass](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-tokenClass.JPG?raw=true)

- the goal of lexical analysis is to classify progarm substring according to role(token class) and communicate tokens to the parser

![goalOfLexicalAnalysis](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-goalOfLexicalAnalysis.JPG?raw=true)

- **to summarise: an implementation must do two thing:**
    + **Recognize substrings corresponding to tokens(lexeme).**
    + **Identity the token class of each lexeme.**   
    **<token class, lexeme\>**
#### Lexical analysis examples 
<small>FORTRAN rule:whitespacei is insignificant  
        <u>VAR 1</u> is the same as <u>VA R1</u>  
        <u>DO 5 I = 1,25</u> (a do loop, I from 1 to 25, 5 is a label)  
        <u>DO 5 I = 1.25</u> (assign, do5I equals to 1.25)  
        we need to lookahead
</small>

1. **The goal is to partition the string. This is implemented by reading left-to-right, recognizing one token at a time.**
2. **"Lookahead" may be required to decide where one token ends and the next token begins. "Lookahead" is always needed, but we want to minimize it.**

<small>PL/1(Programming Language 1) keywords are not reserved  
    <u>IF ELSE THEN THEN=ELSE; ELSE ELSE = THEN</u>   
    <u>DECLARE(ARG1,...,ARGN)</u> (is DECLARE a keyword or array   reference?)(AGR1,...,ARGN: unbounded lookahead)  
<br/>
C++ template syntax: <u>Foo <Bar></u>  
C++ stream syntax: <u>cin >> var;</u>  
<u>Foo < Bar < Bazz ??</u> (>> means ?)
</small>
#### Regular Languages
- Lexical structure = token classes
- We must say what set of strings is in a token class——use regular languages.
- **To define regular languages, we generally use regular expression.**
    + **two base cases:**
        * **Single character, e.g: 'c'={"c"}**
        * **Epsilon,  ε={""}, ε≠Ø**
    + **there compound expressions:**
        * **union:           A+B={a|a∈A} U {b|b∈B}**
        * **concatenation:  AB={ab|a∈A ^ b∈B}**
        * **iteration:       A<sup>*</sup>=U<sub>i>=0</sub>A<sup>i</sup>, (A<sup>0</sup>）=ε**
- **Def. The regular expressions over Σ are the smallest set of expression including.**
    + **R = ε**  
    **&nbsp;&nbsp; |'c'**    
    **&nbsp;&nbsp; | R+R**    
    **&nbsp;&nbsp; | RR**  
    **&nbsp;&nbsp; | R<sup>*</sup>**

<small>Σ={0,1}  
1<sup>\*</sup> = U<sub>i>=0</sub>1<sup>i</sup>="" + 1 + 11 + 111 + ... = all strings of 1's  
(1+0) = {ab|a∈1+0 ^ b∈1} = {11，01}  
0<sup>\*</sup>+1<sup>\*</sup> = {0<sup>i</sup>|i>=0} U {1<sup>\*</sup>|i>=0}   
(0+1)<sup>\*</sup> = U<sub>i>=0</sub>(0+1)<sup>i</sup>="", 0+1, (0+1)(0+1), (0+1)(0+1)(0+1), ... = all strings of 0's and 1's = Σ<sup>\*</sup>
</small>
#### Formal Languages
- Def. Let Σ be a set of characters(an alphabet).
- A language over Σ is a set of strings of characters drawn from Σ.

| ALphabet | Language |
| - | - |
| English characters | English sentences |
| ASCII | C programs |

![meaningFunction](https://github.com/Austen-hub/Austen-hub.github.io/blob/master/basicCourse/images/Compilers-meaningFunction.JPG?raw=true)

- Why user a meaning function?
    + Makes clear what is syntax, what is semantics.
    + Allows us to consider notation as a separate issue.
    + Because expresions and meanings are ont 1-1.(expressions and meanings are many to one. never one to many!!!)
        * <small>1,2,3,4,5,6... vs. I,II,II,IV,V,VI,...</small>(two systems.different notation, the same meaning.)
        * <small>0<sup>\*</sup> = 0+0<sup>\*</sup> = ε+00<sup>\*</sup> = ε+0+0<sup>\*</sup> = ...</small>
#### Lexical Specifications
- Keyword: "if" or "else" or "then" or ...
    + 'i''f' + 'e''l''s''e'  + …   =     'if' + 'else' + …
- Integer: a non-empty string of digits
    + digit<sup>\+</sup>=digit digit<sup>\*</sup>, digit= '0' + '1' + '2' + '3' + '4' + '5' + '6' + '7' + '8' + '9'
    + (AA<sup>\*</sup>=A<sup>\+</sup>)
- Indentifier: strings of letters or digits, starting with a letter
    + letter(letter + digit)<sup>\*</sup>, letter = [a-z | A-Z] =  'a' + 'b' + 'c' + … + 'x' + 'y' + 'z'  + 'A' + … +'Z'
- Whitespace: a non-empty sequence of blanks, newlines, tabs
    + ('' + '\n' + '\t')<sup>\+</sup>

<small>
anyone@cs.stanford.edu(to simplify, only have letters)  
&nbsp;&nbsp;letter<sup>\+</sup>@letter<sup>\+</sup>.letter<sup>\+</sup>.letter<sup>\+</sup>  
PASCAL Language:
&nbsp;&nbsp;digit = '0' + '1' + '2' + '3' + '4' + '5' + '6' + '7' + '8' + '9'  
&nbsp;&nbsp;digits = digit<sup>\+</sup>  
&nbsp;&nbsp;opt_fractioin = ('.'digits) + ε  
&nbsp;&nbsp;opt_exponent = ('E'('+'+'-'+ε)digits)+ε  
&nbsp;&nbsp;num = digits opt_fraction opt_exponent
</small>

- Regular expressions describe many useful language.
    + email, phone number, file names,...
- Regular languages are a language specification.
    + we still need an implementation.
- Next time: Give a string s and a rexpression R, is s∈L(R)?