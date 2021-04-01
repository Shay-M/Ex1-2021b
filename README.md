# Ex1-2021b
FLEX++ - a lexical analyzer generator

FLEX++ - a lexical analyzer generator

Tokens of a language are defined by regular expressions. 
They can be recognized using DFA (Deterministic Finite Automata).

There exist well defined algorithms for construction of DFA from regular expressions  this is a mechanical task that can be performed by computer.

FLEX is an automatic lexical analyzer generator. It translates regular expressions into DFA. 

FLEX is a LEX version for PC. LEX is written in C for UNIX operating systems in 70th. 


	









	









	








 
Structure of input file for FLEX

FLEX input file is divided into 4 basic parts: 


%{ 
C declarations                      
%}

Lex definitions

%%

Lex translation rules

%%
C user subroutines



Declarations part, definition part and user subroutines part are optional.

Declarations part contains global C code; this part starts with   %{   and ends with   %}  (each of them should appear at the beginning of a separate line). For example:
 %{ 
#include <math.h> 
%}

Definition part contains regular definitions. For example:
DIGIT    [0-9]
ID           [a-z][a-z0-9]*

User subroutines part contains C code that is copied to lexyy.c as it is. It may contain functions used in actions, or functions used by parser.

The first separator (%%) is essential; the second separator (%%) is not needed if the third part is empty. 

Each translation rule has the following format:
pattern    action
where pattern is a regular expression. During lexical analysis, each time when a word matching the pattern is found, the corresponding action  is applied. Multiple actions should be placed between { and }.

For example:
%%
\n 	                  printf("\n");

[1-9]{DIGIT}*    {printf("An integer: %s", yytext);
		             return INT_NUM;}
%%






 
Some extended regular expressions

[abcd]    a “character class” - means a | b | c | d
[a-d]       a “character class” with range in it - means [abcd]
[^A-Z]    a “negated character class” – means any character except 
               those  that belong to the class (here – any character except 
               upper-case letters). NOTE:  ^ should appear within the 
               brackets [..]
.      means any character except new line (i.e. except  \n)
a?   means zero or one a’s
a{1,3} - means from one to three a’s
r{3} – means r exactly three times – equals to rrr
r/s - means r but only followed by s
^r  - means r but only if r is in the beginning of an input line
r$ - means r but only at the end of the input line
\   is used as escape character
<<EOF>> an end-of-file sign


Examples
Pattern				Meaning
“for”					reserved word for
“--”					decrement operator
[A-Za-z_][A-Za-z0-9_]*  	C identifiers
“/*”.*”*/”				single line comments
“//”.*					C++ comments
[1-9][0-9]*				integer constants
[^A-Z\n]				any letter except upper case letters or 
new-line			

 

Ambiguty

Laws of resolution:
-	FLEX always chooses the pattern that represents the longest possible input string. 
-	If two patterns represent the same string, the first pattern in the list is chosen.

For example: if there is a word int and rules are written in the following order:
[a-z]+	 {return ID;}
int	  	 {return INT;}
then action of the first rule will be executed.


Global variables

FLEX operates with several global variables; their names start with yy. For example: when a pattern is matched, the text corresponding to it (i.e. the lexeme) is put into yytext.

•	yytext – holds token’s lexeme 
•	yyleng – holds token’s length
•	yyin – input stream (default is keyboard)
•	yyout – output stream (default is screen)
  

Some FLEX  actions and functions

•	yylex() – actions in rules can include return statements; returned value goes to the routine yylex. Each time yylex is called, it continues to process tokens from where it last left off until it either reaches end of file or return statement.
•	yyless(n) – returns all but the first n characters of current token back to the input stream
•	ECHO – copies yytext to scanner’s output stream
•	BEGIN() – begins start condition
 
Some FLEX examples

Program that copies input file to the screen:












where  yyin  is name of input stream.

 
Program that adds line numbers to the input file:














 
Cleaning comments from input file and printing the result to output file (using start conditions):


where 
%x condition_name    - an exclusive start condition (only rules with start condition <condition_name> are active)

%s condition_name    - an inclusive start condition (rules with and without start condition <condition_name> are active)

To start a condition write BEGIN(condition_name). 
To remove it write BEGIN(0) or BEGIN(INITIAL).

Start condition allows you to write a mini-scanner inside your scanner.
 
 
Input file: hello.txt				Output file: out.txt




In our translation, we printed new line after one line comment and after multi-line comment. 
Printing all tokens of input file, written in a language of arithmetic expressions, to output file:

where DIGIT is a Flex definition for a group of  digits.

Input file: input.txt      		  Output file: out.tok
















To run the generated lexical analyzer, add 
int yywrap(){return 1;}
to your lexyy.c file.

