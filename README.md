# Compiler Design Practicals
<p>1. Design a Lexical analyzer to recognize a few pattern in C.
The lexical analyzer should ignore redundant spaces, tabs and new lines, comments etc.</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <stdlib.h>
int COMMENT = 0;
%}

identifier [a-zA-Z][a-zA-Z0-9]*

%%
#.*                                 { if(!COMMENT) printf("\n%s is a preprocessor directive", yytext); }
int|float|char|double|while|for|struct|typedef|do|if|break|continue|void|switch|return|else|goto { if(!COMMENT) printf("\n\t%s is a keyword", yytext); }
"/*"                                { COMMENT = 1; printf("\n\tCOMMENT BEGINS"); }
"*/"                                { COMMENT = 0; printf("\n\tCOMMENT ENDS"); }
{identifier}\(                      { if(!COMMENT) printf("\nFUNCTION \n\t%s", yytext); }
"{"                                 { if(!COMMENT) printf("\n BLOCK BEGINS"); }
"}"                                 { if(!COMMENT) printf("\n BLOCK ENDS "); }
{identifier}(\[[0-9]*\])?           { if(!COMMENT) printf("\n %s IDENTIFIER", yytext); }
\".*\"                              { if(!COMMENT) printf("\n\t %s is a STRING", yytext); }
[0-9]+                              { if(!COMMENT) printf("\n %s is a NUMBER ", yytext); }
\)(\:)?                             { if(!COMMENT) { printf("\n\t"); ECHO; printf("\n"); } }
\(                                  { if(!COMMENT) ECHO; }
=                                   { if(!COMMENT) printf("\n\t %s is an ASSIGNMENT OPERATOR", yytext); }
\<=|\>=|\<|==|\>                    { if(!COMMENT) printf("\n\t%s is a RELATIONAL OPERATOR", yytext); }
[ \t\n]+                            { /* Ignore whitespace */ }
.                                   { /* Ignore other characters like commas or semicolons */ }
%%

int main(int argc, char **argv) {
    FILE *file;
    file = fopen("var.c", "r");
    if (!file) {
        printf("Could not open the file 'var.c'\n");
        exit(0);
    }
    yyin = file;
    yylex();
    printf("\n");
    return 0;
}

int yywrap() {
    return 1;
}
```
### var.c 

```c
#include<stdio.h>

void main() {
    int a,b,c;
    a=1;
    b=2;
    c=a+b;
    printf("Sum: %d",c);
}
```

### Compilation and Running Commands
```bash
usr@user:~$ flex lex.l
usr@user:~$ gcc lex.yy.c -o lex_analyzer
usr@user:~$ ./lex_analyzer
```

### Sample Output
<p>This Output based on what kind of c program you give for an input. In this program you refer var.c as a input c program.</p>

```text

#include<stdio.h> is a preprocessor directive
	void is a keyword
FUNCTION 
	main(
	)

 BLOCK BEGINS
	int is a keyword
 a IDENTIFIER
 b IDENTIFIER
 c IDENTIFIER
 a IDENTIFIER
	 = is an ASSIGNMENT OPERATOR
 1 is a NUMBER 
 b IDENTIFIER
	 = is an ASSIGNMENT OPERATOR
 2 is a NUMBER 
 c IDENTIFIER
	 = is an ASSIGNMENT OPERATOR
 a IDENTIFIER
 b IDENTIFIER
FUNCTION 
	printf(
	 "Sum: %d" is a STRING
 c IDENTIFIER
	)

 BLOCK ENDS 
```
---

<p>2.Implement the lexical analyzer using JLex, flex or other lexical analyzer generating tools.</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <stdlib.h>
int COMMENT = 0;
%}

identifier [a-zA-Z][a-zA-Z0-9]*

%%
#.*                                 { if(!COMMENT) printf("\n%s is a preprocessor directive", yytext); }
int|float|char|double|while|for|struct|typedef|do|if|break|continue|void|switch|return|else|goto { if(!COMMENT) printf("\n\t%s is a keyword", yytext); }
"/*"                                { COMMENT = 1; printf("\n\tCOMMENT BEGINS"); }
"*/"                                { COMMENT = 0; printf("\n\tCOMMENT ENDS"); }
{identifier}\(                      { if(!COMMENT) printf("\nFUNCTION \n\t%s", yytext); }
"{"                                 { if(!COMMENT) printf("\n BLOCK BEGINS"); }
"}"                                 { if(!COMMENT) printf("\n BLOCK ENDS "); }
{identifier}(\[[0-9]*\])?           { if(!COMMENT) printf("\n %s IDENTIFIER", yytext); }
\".*\"                              { if(!COMMENT) printf("\n\t %s is a STRING", yytext); }
[0-9]+                              { if(!COMMENT) printf("\n %s is a NUMBER ", yytext); }
\)(\:)?                             { if(!COMMENT) { printf("\n\t"); ECHO; printf("\n"); } }
\(                                  { if(!COMMENT) ECHO; }
=                                   { if(!COMMENT) printf("\n\t %s is an ASSIGNMENT OPERATOR", yytext); }
\<=|\>=|\<|==|\>                    { if(!COMMENT) printf("\n\t%s is a RELATIONAL OPERATOR", yytext); }

[ \t\n]+                            { /* Ignore redundant spaces, tabs, and newlines */ }
.                                   { /* Ignore other characters like commas or semicolons */ }
%%

int main(int argc, char **argv) {
    FILE *file;
    file = fopen("var.c", "r");
    if (!file) {
        printf("Could not open the file 'var.c'\n");
        exit(0);
    }
    yyin = file;
    yylex();
    printf("\n");
    return 0;
}

int yywrap() {
    return 1;
}

```
### var.c
```c
#include<stdio.h>

void main() {
    int a,b,c;
    a=1;
    b=2;
    c=a+b;
    printf("Sum: %d",c);
}
```
### Compilation and Running Commands
```bash
usr@user:~$ flex lex.l
usr@user:~$ gcc lex.yy.c -o lex_analyzer
usr@user:~$ ./lex_analyzer
```

### Sample Output

```text
#include<stdio.h> is a preprocessor directive
	void is a keyword
FUNCTION 
	main(
	)

 BLOCK BEGINS
	int is a keyword
 a IDENTIFIER
 b IDENTIFIER
 c IDENTIFIER
 a IDENTIFIER
	 = is an ASSIGNMENT OPERATOR
 1 is a NUMBER 
 b IDENTIFIER
	 = is an ASSIGNMENT OPERATOR
 2 is a NUMBER 
 c IDENTIFIER
	 = is an ASSIGNMENT OPERATOR
 a IDENTIFIER
 b IDENTIFIER
FUNCTION 
	printf(
	 "Sum: %d" is a STRING
 c IDENTIFIER
	)

 BLOCK ENDS
```
---

<hr>

<p>3. Write a program to implement to recognize a valid arithmetic expression that uses operator +, – ,
* and /.</p>

### lex.l
```lex
%{
#include <stdio.h>
#include "y.tab.h"
%}

%%
[a-zA-Z]    { return VARIABLE; }
[0-9]+      { return NUMBER; }
[\t ]       { ; }       /* Skip tabs and spaces */
[\n]        { return 0; }       /* End parsing on newline */
.           { return yytext[0]; }      /* Return operators like +, -, *, /, (, ) directly */
%%

int yywrap() {
    return 1;
}

```

### parser.y
```yacc
%{
#include <stdio.h>
#include <stdlib.h>

int yylex(void);
void yyerror(const char *s);
%}

%token NUMBER VARIABLE
%left '+' '-'
%left '*' '/' '%'
%left '(' ')'

%%
S : VARIABLE '=' E {
    printf("\nEntered arithmetic expression is Valid\n\n");
    return 0;
}
;

E : E '+' E
  | E '-' E
  | E '*' E
  | E '/' E
  | E '%' E
  | '(' E ')'
  | NUMBER
  | VARIABLE
  ;
%%

int main() {
    printf("\nEnter Any Arithmetic Expression (e.g., a=5+3*b):\n");
    yyparse();
    return 0;
}

void yyerror(const char *s) {
    printf("\nEntered arithmetic expression is Invalid\n\n");
}

```

### Compilation and Running Commands
```bash
usr@user:~$ yacc -d parse.y
usr@user:~$ flex lex.l
usr@user:~$ gcc y.tab.c lex.yy.c -o arith_analyzer
usr@user:~$ ./arith_analyzer

```
### Sample Output 
```text

Enter Any Arithmetic Expression (e.g., a=5+3*b):
a = b + c * 15

Entered arithmetic expression is Valid

```

<hr>

<p>4. Write a program to implement a Calculator using LEX and YACC.</p>

### lex.l
```lex
%{
#include<stdio.h>
#include<stdlib.h>
#include "y.tab.h"
%}

%%
[0-9]+	{ yylval = atoi(yytext); return NUMBER; }
[ \t]   { ; } 
\n      { return NEWLINE; }
.       { return yytext[0]; }
%%

int yywrap() {
   return 1;
}


```
### parser.y
```yacc
%{
#include <stdio.h>
#include <stdlib.h>

int yylex(void);
void yyerror(const char *s);
%}

%token NUMBER NEWLINE
%left '+' '-'
%left '*' '/'

%%
input:
    /* empty */
    | input line
    ;

line:
    NEWLINE
    | expr NEWLINE { printf("Result = %d\n\n", $1); }
    | error NEWLINE { yyerrok; }
    ;

expr:
    NUMBER          { $$ = $1; }
    | expr '+' expr { $$ = $1 + $3; }
    | expr '-' expr { $$ = $1 - $3; }
    | expr '*' expr { $$ = $1 * $3; }
    | expr '/' expr { 
                        if ($3 == 0) {
                            yyerror("Divide by zero error");
                            $$ = 0;
                        } else {
                            $$ = $1 / $3; 
                        }
                    }
    | '(' expr ')'  { $$ = $2; }
    ;
%%

int main() {
    printf("\nEnter arithmetic expressions to calculate (e.g., 5 + 3 * 2):\n");
    yyparse();
    return 0;
}

void yyerror(const char *s) {
    printf("Error: %s\n\n", s);
}
```

### Compilation and Running Commands
```bash
usr@user:~$ yacc -d parser.y
usr@user:~$ flex lex.l
usr@user:~$ gcc y.tab.c lex.yy.c -o calculator
usr@user:~$ ./calculator
```
### Sample Output 
```text
Enter arithmetic expressions to calculate (e.g., 5 + 3 * 2):
2 + 2
Result = 4

```

<hr>

<p>5. Write a Program to recognize a valid variable which starts with a letter followed by any number
of letters or digits.</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <stdlib.h>
#include "y.tab.h"
%}

%%
[a-zA-Z][a-zA-Z0-9]*    { return VARIABLE; }
[ \t]                   { ; } /* Skip spaces and tabs */
\n                      { return NEWLINE; }
.                       { return yytext[0]; } /* Pass invalid single characters to Yacc */
%%

int yywrap() {
    return 1;
}

```
### parser.y
```yacc
%{
#include <stdio.h>
#include <stdlib.h>

int yylex(void);
void yyerror(const char *s);
%}

%token VARIABLE NEWLINE

%%
input:
    /* empty */
    | input line
    ;

line:
    NEWLINE
    | VARIABLE NEWLINE { printf("Result: Valid Variable Identifier\n\n"); }
    | error NEWLINE    { yyerrok; }
    ;
%%

int main() {
    printf("\nEnter variable names to validate:\n");
    yyparse();
    return 0;
}

void yyerror(const char *s) {
    printf("Result: Invalid Variable Identifier\n\n");
}

```

### Compilation and Running Commands
```bash
usr@user:~$ yacc -d parse.y
usr@user:~$ flex lex.l
usr@user:~$ gcc y.tab.c lex.yy.c -o var_analyzer
usr@user:~$ ./var_analyzer
```
### Sample Output 
```text

Enter variable names to validate:
name
Result: Valid Variable Identifier

age
Result: Valid Variable Identifier

12height
Result: Invalid Variable Identifier

```

<hr>

<p>6. Write a LEX Program to convert the substring abc to ABC from the given input string..</p>

### lex.l
```lex
%{
#include <stdio.h>
%}

%%
"abc"   { printf("ABC"); }     /* Replace 'abc' with 'ABC' */
.       { printf("%s", yytext); }     /* Print all other characters as they are */
\n      { printf("%s", yytext); }     /* Print newlines perfectly */
%%

int main() {
    printf("Enter the input string:\n");
    yylex();
    return 0;
}

int yywrap() {
    return 1;
}

```

### Compilation and Running Commands
```bash
usr@user:~$ flex lex.l
usr@user:~$ gcc lex.yy.c -o abc_validate
usr@user:~$ ./abc_validate

```
### Sample Output 
```text
Enter the input string:
martabc
martABC

abc
ABC

abcwelcome
ABCwelcome
```
<hr>

7. Write a C Program to implement NFAs that recognize identifiers, constants, and operators of the
mini language.</p>

### nfa_recognizer.c
```c
#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define MAX_STATES 5
#define MAX_INPUT 50

// Function to simulate NFA matching
int match_nfa(char *str, int type) {
    int states[MAX_STATES] = {0}; // Active states array
    int num_active = 0;
    
    // Initialize the starting state
    states[num_active++] = 0; 
    int len = strlen(str);

    for (int i = 0; i < len; i++) {
        char ch = str[i];
        int input_cls = -1;

        // Classify the current character
        if (isalpha(ch)) input_cls = 0;      // Letter
        else if (isdigit(ch)) input_cls = 1; // Digit
        else if (ch == '+' || ch == '-' || ch == '*' || ch == '/') input_cls = 2; // Operator
        else return 0; // Unknown symbol

        int next_states[MAX_STATES];
        int next_count = 0;

        // Compute state movements based on token types
        for (int j = 0; j < num_active; j++) {
            int curr = states[j];

            if (type == 1) { // --- Identifier NFA ---
                if (curr == 0 && input_cls == 0) next_states[next_count++] = 1;
                if (curr == 1 && (input_cls == 0 || input_cls == 1)) next_states[next_count++] = 1;
            } 
            else if (type == 2) { // --- Constant NFA ---
                if (curr == 0 && input_cls == 1) next_states[next_count++] = 1;
                if (curr == 1 && input_cls == 1) next_states[next_count++] = 1;
            } 
            else if (type == 3) { // --- Operator NFA ---
                if (curr == 0 && input_cls == 2) next_states[next_count++] = 1;
            }
        }

        if (next_count == 0) return 0; // Trap state hit
        
        // Update active states list
        num_active = next_count;
        for (int j = 0; j < num_active; j++) states[j] = next_states[j];
    }

    // Check if any active state ended in an accepting/final state (State 1)
    for (int i = 0; i < num_active; i++) {
        if (states[i] == 1) return 1;
    }
    return 0;
}

int main() {
    char input[MAX_INPUT];
    printf("\nNFA Lexical Recognizer (Mini-Language) \n");
    printf("Enter a token to analyze: ");
    scanf("%s", input);

    if (match_nfa(input, 1)) {
        printf("Result: [IDENTIFIER]\n\n");
    } else if (match_nfa(input, 2)) {
        printf("Result: [CONSTANT]\n\n");
    } else if (match_nfa(input, 3)) {
        printf("Result: [OPERATOR]\n\n");
    } else {
        printf("Result: [INVALID TOKEN]\n\n");
    }
    return 0;
}

```

### Compilation and Running Commands
```bash
usr@user:~$ gcc nfa_recognizer.c -o nfa_recognizer
usr@user:~$ ./nfa_recognizer

```
### Sample Output 
```text
NFA Lexical Recognizer (Mini-Language) 
Enter a token to analyze: welcome
Result: [IDENTIFIER]

```

<hr>

<p>8. Write a C Program to implement DFAs that recognize identifiers, constants, and operators of the
mini language.</p> 

### dfa_recognizer.c
```c
#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define MAX_INPUT 50

void recognize_token_dfa(char *str) {
    int state = 0; // Start state
    int len = strlen(str);

    for (int i = 0; i < len; i++) {
        char ch = str[i];

        switch (state) {
            case 0: // Starting state
                if (isalpha(ch)) {
                    state = 1; // Letter -> Identifier state
                } else if (isdigit(ch)) {
                    state = 2; // Digit -> Constant state
                } else if (ch == '+' || ch == '-' || ch == '*' || ch == '/') {
                    state = 3; // Operator character -> Operator state
                } else {
                    state = 4; // Trap state for invalid inputs
                }
                break;

            case 1: // Inside Identifier state
                if (isalpha(ch) || isdigit(ch)) {
                    state = 1; // Stay in identifier state
                } else {
                    state = 4; // Go to trap state
                }
                break;

            case 2: // Inside Constant state
                if (isdigit(ch)) {
                    state = 2; // Stay in constant state
                } else {
                    state = 4; // Go to trap state
                }
                break;

            case 3: // Inside Operator state (Expects only single character)
                state = 4; // Any extra character breaks the single operator definition
                break;

            case 4: // Trap state
                break;
        }
    }

    // Evaluate the final state configuration
    if (state == 1) {
        printf("Result: [IDENTIFIER]\n\n");
    } else if (state == 2) {
        printf("Result: [CONSTANT]\n\n");
    } else if (state == 3) {
        printf("Result: [OPERATOR]\n\n");
    } else {
        printf("Result: [INVALID TOKEN]\n\n");
    }
}

int main() {
    char input[MAX_INPUT];
    printf("\nDFA Lexical Recognizer (Mini-Language) \n");
    printf("Enter a token to analyze: ");
    scanf("%s", input);

    recognize_token_dfa(input);
    return 0;
}

```

### Compilation and Running Commands
```bash
usr@user:~$ gcc nfa_recognizer.c -o dfa_recognizer
usr@user:~$ ./dfa_recognizer

```
### Sample Output 
```text
DFA Lexical Recognizer (Mini-Language) 
Enter a token to analyze: 1234
Result: [CONSTANT]

```

<hr>

<p>9. Write a program to Implement Simple Code Optimization Techniques using Constant Folding</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <stdlib.h>
%}

/* Matches digit strings, identifiers, spaces, and operators */

NUM     [0-9]+
ID      [a-zA-Z_][a-zA-Z0-9_]*
WS      [ \t]*

%%

{ID}{WS}"="{WS}{NUM}{WS}[+\-*/]{WS}{NUM} {
    char var[20], op;
    int val1, val2, res = 0;

    /* Parse structural values natively from the matched text */

    sscanf(yytext, "%s = %d %c %d", var, &val1, &op, &val2);

    /* Evaluate the constant math operation */

    switch(op) {
        case '+': res = val1 + val2; break;
        case '-': res = val1 - val2; break;
        case '*': res = val1 * val2; break;
        case '/': if(val2 != 0) res = val1 / val2; break;
    }

    printf("Optimized Code: %s = %d\n", var, res);
}

\n    { /* Ignore newlines */ }
.     { /* Ignore unmatched characters */ }

%%

int yywrap() { return 1; }

int main() {
    printf("Enter a three-address statement (e.g., x = 15 * 4):\n");
    yylex();
    return 0;
}

```

### Compilation and Running Commands
```bash
usr@user:~$ flex lex.l
usr@user:~$ gcc lex.yy.c -o const_fold
usr@user:~$ ./const_fold

```
### Sample Output 
```text
Enter a three-address statement (e.g., x = 15 * 4):
a = 10 + 15
Optimized Code: a = 25

```

<hr>

<p>10. Write a program to Implement the back end of the compiler which takes the three address code
and produces the 8086 assembly language instructions that can be assembled and run using a
8086 assembler. The target assembly instructions can be simple move, add, sub, jump. Also
simple addressing modes are used.</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_LINE 100

// Buffer to match the exact array storage of your C program
char buffer[50][MAX_LINE];
int line_count = 0;

void process_buffer();
%}

/* Definitions */
ID      [a-zA-Z_][a-zA-Z0-9_]*
OPERAND [a-zA-Z0-9_]+
WS      [ \t]*

%%

"exit" { 
    /* Triggers processing exactly like typing 'exit' in your C code */
    process_buffer(); 
    return 0; 
}

.* {
    /* Capture the entire line and store it into the buffer */
    if (line_count < 50) {
        strcpy(buffer[line_count++], yytext);
    }
}

\n { /* Ignore newline tokens to cleanly separate strings */ }

%%

void generate_8086(char *tac_line) {
    char var[20], op1[20], op[20], op2[20];
    int tokens;

    // 1. Unconditional jump
    if (sscanf(tac_line, "goto %s", op1) == 1) {
        printf("\tJMP %s\n", op1);
        return;
    }

    // 2. Labels
    if (strchr(tac_line, ':') != NULL) {
        printf("%s\n", tac_line);
        return;
    }

    // 3. Binary operations
    tokens = sscanf(tac_line, "%s = %s %s %s", var, op1, op, op2);

    if (tokens == 4) {
        printf("\tMOV AX, %s\n", op1);
        if (strcmp(op, "+") == 0) {
            printf("\tADD AX, %s\n", op2);
        } else if (strcmp(op, "-") == 0) {
            printf("\tSUB AX, %s\n", op2);
        } else {
            printf("\t; Unsupported operator %s\n", op);
            return;
        }
        printf("\tMOV %s, AX\n", var);
    } 
    // 4. Simple assignment
    else if (tokens == 2 || (tokens == 3 && strcmp(op, "=") == 0)) {
        sscanf(tac_line, "%s = %s", var, op1);
        printf("\tMOV AX, %s\n", op1);
        printf("\tMOV %s, AX\n", var);
    }
}

void process_buffer() {
    printf("\n--- Generated 8086 Assembly Instructions ---\n\n");
    for (int i = 0; i < line_count; i++) {
        printf("  %s\n", buffer[i]); // Matches your original C program's comment trace
        generate_8086(buffer[i]);
    }
    printf("\n----------------------------------------------------\n");
}

int yywrap() { 
    return 1; 
}

int main() {
    printf("\n----------------------------------------------------\n");
    printf("   8086 Assembly Code Generator (Compiler Back End)   \n");
    printf("------------------------------------------------------\n");
    printf("Enter Three-Address Code (Type 'exit' to generate & stop):\n\n");
    
    yylex();
    return 0;
}

```

### Compilation and Running Commands
```bash
usr@user:~$ flex lex.l
usr@user:~$ gcc lex.yy.c -o lex
usr@user:~$ ./lex

```
### Sample Output 
```text
----------------------------------------------------
   8086 Assembly Code Generator (Compiler Back End)   
------------------------------------------------------
Enter Three-Address Code (Type 'exit' to generate & stop):

a = b + c
c = a + d
d = f
exit

--- Generated 8086 Assembly Instructions ---

  a = b + c
	MOV AX, b
	ADD AX, c
	MOV a, AX
  c = a + d
	MOV AX, a
	ADD AX, d
	MOV c, AX
  d = f
	MOV AX, f
	MOV d, AX

----------------------------------------------------

```

<hr>

<p>11. Write a lex program to find out total number of vowels, and consonants from the given input
sting.</p>

### lex.l
```lex
%{
#include <stdio.h>
int vowels = 0;
int consonants = 0;
%}

%%
[aeiouAEIOU]    { vowels++; }
[a-zA-Z]        { consonants++; }
[ \t]           { ; } /* Skip spaces and tabs */

\n              { 
                    /* Print the results as soon as the user presses Enter */
                    printf("\n--- Results ---");
                    printf("\nTotal Number of Vowels    : %d", vowels);
                    printf("\nTotal Number of Consonants: %d\n\n", consonants);
                    
                    /* Exit the program after printing the first line's result */
                    return 0; 
                }

.               { ; }
%%

int main() {
    printf("\nEnter the input string: ");
    yylex();
    return 0;
}

int yywrap() {
    return 1;
}

```

### Compilation and Running Commands
```bash
usr@user:~$ flex lex.l
usr@user:~$ gcc lex.yy.c -o vowel_count
usr@user:~$ ./vowel_count
```
### Sample Output 
```text
Enter the input string: welcome                

--- Results ---
Total Number of Vowels    : 3
Total Number of Consonants: 4

```

<hr>

<p>12. Write a program to implement to recognize a valid control structures syntax of C language using
For loop and while loop.</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <stdlib.h>
#include "y.tab.h"
%}

%%
"for"                               { return FOR; }
"while"                             { return WHILE; }
"int"|"float"|"char"|"double"       { return TYPE; } /* Recognizes data types */
[a-zA-Z][a-zA-Z0-9]*                { return ID; }
[0-9]+                              { return NUM; }
"<="|">="|"<"|">"|"=="|"!="         { return RELOP; }
"++"|"--"                           { return INCDEC; }
[ \t\n]                             { ; } /* Skip whitespace */
.                                   { return yytext[0]; } /* Return single characters directly */
%%

int yywrap() {
    return 1;
}

```
### parser.y
```yacc
%{
#include <stdio.h>
#include <stdlib.h>

int yylex(void);
void yyerror(const char *s);
%}

%token FOR WHILE TYPE ID NUM RELOP INCDEC

%%
start : loop { printf("\nResult: Valid Loop Syntax\n\n"); return 0; }
      ;

loop  : for_loop
      | while_loop
      ;

// --- Updated For Loop Structure to support optional TYPE declaration ---
for_loop : FOR '(' init ';' condition ';' increment ')' block
         ;

init : TYPE ID '=' NUM   /* Handles: int i = 0 */
     | ID '=' NUM        /* Handles: i = 0 */
     | ID '=' ID
     | /* empty */
     ;

condition : ID RELOP NUM
          | ID RELOP ID
          | /* empty */
          ;

increment : ID INCDEC
          | ID '=' ID '+' NUM
          | ID '=' ID '-' NUM
          | /* empty */
          ;

while_loop : WHILE '(' condition ')' block
           ;

block : ';'                       
      | statement ';'             
      | '{' statement_list '}'    
      ;

statement_list : statement_list statement ';'
               | /* empty */
               ;

statement : ID '=' NUM
          | ID '=' ID
          | ID INCDEC
          ;
%%

int main() {
    printf("\nEnter a C For or While loop structure to validate:\n");
    yyparse();
    return 0;
}

void yyerror(const char *s) {
    printf("\nResult: Invalid Loop Syntax\n\n");
}

```

### Compilation and Running Commands
```bash
usr@user:~$ yacc -d parser.y
usr@user:~$ flex lex.l
usr@user:~$ gcc y.tab.c lex.yy.c -o syntax_analyzer
usr@user:~$ ./syntax_analyzer
```
### Sample Output 
```text
Enter a C For or While loop structure to validate:
for(int i=0; i<=10; i++) { a++; }

Result: Valid Loop Syntax


```

<hr>

<p>13. Write a program to Convert the BNF rules into Yacc form and write code to generate abstract
syntax tree.</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "y.tab.h"
%}

%%
[0-9]+          { yylval.str = strdup(yytext); return NUMBER; }
[ \t]           { ; } /* Skip spaces */
\n              { return NEWLINE; }
.               { return yytext[0]; }
%%

int yywrap() {
    return 1;
}

```

### parser.y
```yacc
%{
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Define the structure for Abstract Syntax Tree Nodes
struct ast {
    char *val;
    struct ast *left;
    struct ast *right;
};

struct ast* make_node(char *val, struct ast *left, struct ast *right);
void print_ast(struct ast *tree, int level);
int yylex(void);
void yyerror(const char *s);
%}

%union {
    char *str;
    struct ast *node;
}

%token <str> NUMBER
%token NEWLINE
%type <node> expr term factor

%%
// BNF Grammar Rules in Yacc Format
input  : /* empty */
       | input line
       ;

line   : NEWLINE
       | expr NEWLINE { 
            printf("\n--- Generated Abstract Syntax Tree (Rotated 90 Deg) ---\n\n");
            print_ast($1, 0); 
            printf("\nEnter next expression:\n");
         }
       ;

expr   : expr '+' term   { $$ = make_node("+", $1, $3); }
       | expr '-' term   { $$ = make_node("-", $1, $3); }
       | term            { $$ = $1; }
       ;

term   : term '*' factor { $$ = make_node("*", $1, $3); }
       | term '/' factor { $$ = make_node("/", $1, $3); }
       | factor          { $$ = $1; }
       ;

factor : '(' expr ')'    { $$ = $2; }
       | NUMBER          { $$ = make_node($1, NULL, NULL); }
       ;
%%

// Helper to allocate memory and initialize an AST Node
struct ast* make_node(char *val, struct ast *left, struct ast *right) {
    struct ast *new_node = (struct ast*)malloc(sizeof(struct ast));
    new_node->val = strdup(val);
    new_node->left = left;
    new_node->right = right;
    return new_node;
}

// Function to visualize the tree structure dynamically using indents
void print_ast(struct ast *tree, int level) {
    if (tree == NULL) return;
    
    // Recursive call on right subtree first
    print_ast(tree->right, level + 1);
    
    // Print indentation based on depth levels
    for (int i = 0; i < level; i++) {
        printf("    ");
    }
    printf("%s\n", tree->val);
    
    // Recursive call on left subtree
    print_ast(tree->left, level + 1);
}

int main() {
    printf("\nEnter an arithmetic expression (e.g., 5 + 3 * 2):\n");
    yyparse();
    return 0;
}

void yyerror(const char *s) {
    printf("Syntax Error: %s\n", s);
}

```

### Compilation and Running Commands
```bash
usr@user:~$ yacc -d parse.y
usr@user:~$ flex lex.l
usr@user:~$ gcc y.tab.c lex.yy.c -o abs_tree
usr@user:~$ ./abs_tree
```
### Sample Output 
```text
Enter an arithmetic expression (e.g., 5 + 3 * 2):
4 + 5 * 3

--- Generated Abstract Syntax Tree (Rotated 90 Deg) ---

        3
    *
        5
+
    4


```

<hr>

<p>14. Write a program to implement to recognize a valid control structures syntax of C language using
ifelse, if-else-if and switch-case.</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include "y.tab.h" 
%}

%%
"if"        { return IF; }
"else"      { return ELSE; }
"switch"    { return SWITCH; }
"case"      { return CASE; }
"default"   { return DEFAULT; }
"break"     { return BREAK; }

[a-zA-Z_][a-zA-Z0-9_]* { return ID; }   /* Removed yylval line */
[0-9]+                  { return NUM; }  /* Removed yylval line */

"++"|"--"   { return INCDEC; }
"<="|">="|"<"|">"|"=="|"!=" { return RELOP; }

[ \t]       { ; } 
\n          { return 0; } 
.           { return yytext[0]; }
%%

int yywrap() { return 1; }

```

### parser.y
```yacc
%{
#include <stdio.h>
#include <stdlib.h>

/* Explicit prototypes to resolve compilation warnings */
int yylex(void);
void yyerror(const char *s);
int yyparse(void);
%}

%token IF ELSE SWITCH CASE DEFAULT BREAK ID NUM RELOP INCDEC

/* Dangling else precedence tracking setup */
%nonassoc LOWER_THAN_ELSE
%nonassoc ELSE

%%
start : stmt { printf("\nResult: Valid Syntax\n\n"); return 0; }
      ;

stmt  : IF '(' cond ')' block %prec LOWER_THAN_ELSE
      | IF '(' cond ')' block ELSE block
      | SWITCH '(' ID ')' '{' cases def_part '}'
      ;

cases     : cases CASE NUM ':' block
          | /* empty */
          ;

def_part  : DEFAULT ':' block
          | /* empty */
          ;

cond      : ID RELOP NUM 
          | ID RELOP ID 
          | ID 
          ;

block     : ';' 
          | body ';' 
          | stmt
          | '{' list '}' 
          ;

list      : list body ';' 
          | list stmt
          | list ';'       
          | /* empty */ 
          ;

body      : ID '=' NUM 
          | ID '=' ID 
          | ID '=' ID '+' NUM   /* Added for practical assignments like i = i + 1 */
          | ID '=' ID '-' NUM
          | ID INCDEC           
          | BREAK 
          ;
%%

int main() {
    printf("\nEnter structure to validate:\n");
    yyparse();
    return 0;
}

void yyerror(const char *s) {
    printf("\nResult: Invalid Syntax\n\n");
}


```

### Compilation and Running Commands
```bash
usr@user:~$ yacc -d parse.y
usr@user:~$ flex lex.l
usr@user:~$ gcc y.tab.c lex.yy.c -o cs_validate
usr@user:~$ ./cs_validate
```
### Sample Output 
```text
Enter structure to validate:
if(x > 5) { a = 10; }

Result: Valid Syntax

```

<hr>

<p>15. Write a program to Implement any one storage allocation strategies</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <stdlib.h>

#define MAX 20
int blocks[MAX], processes[MAX];
int nb = 0, np = 0;           
int b_cnt = 0, p_cnt = 0;     

void run_first_fit();
%}

%%
[0-9]+ {
    int val = atoi(yytext);
    
    if (nb == 0) {
        nb = val;
        printf("Enter sizes for the %d memory blocks:\nBlock 1 Size: ", nb);
    } 
    else if (b_cnt < nb) {
        blocks[b_cnt++] = val;
        if (b_cnt < nb) printf("Block %d Size: ", b_cnt + 1);
        else printf("\nEnter the number of processes: ");
    } 
    else if (np == 0) {
        np = val;
        printf("Enter memory sizes required for the %d processes:\nProcess 1 Size: ", np);
    } 
    else if (p_cnt < np) {
        processes[p_cnt++] = val;
        if (p_cnt < np) printf("Process %d Size: ", p_cnt + 1);
        else { run_first_fit(); return 0; }
    }
}
[ \t\n] { ; }
.       { ; }
%%

void run_first_fit() {
    int allocation[MAX], fragments[MAX];
    
    for (int i = 0; i < np; i++) allocation[i] = -1;

    // Exact First Fit Logic
    for (int i = 0; i < np; i++) {
        for (int j = 0; j < nb; j++) {
            if (blocks[j] >= processes[i]) {
                allocation[i] = j;
                fragments[i] = blocks[j] - processes[i];
                blocks[j] = 0; 
                break; 
            }
        }
    }

    // Display the structured results table exactly matching your first C program
    printf("\nProcess No.\tProcess Size\tBlock No.\tFragment Size\n");
    printf("-----------------------------------------------------------------\n");
    for (int i = 0; i < np; i++) {
        printf("%d\t\t%d\t\t", i + 1, processes[i]);
        if (allocation[i] != -1) {
            printf("%d\t\t%d\n", allocation[i] + 1, fragments[i]);
        } else {
            printf("Not Allocated\t-\n");
        }
    }
    printf("\n");
}

int yywrap() { return 1; }

int main() {
    printf("\n=== First Fit Storage Allocation Strategy ===\n");
    printf("Enter the number of memory blocks available: ");
    yylex();
    return 0;
}

```

### Compilation and Running Commands
```bash
usr@user:~$ flex lex.l
usr@user:~$ gcc lex.yy.c -o lex
usr@user:~$ ./lex
```
### Sample Output 
```text
--- First Fit Storage Allocation Strategy ---
Enter the number of memory blocks available: 3
Enter sizes for the 3 memory blocks:
Block 1 Size: 100
Block 2 Size: 200
Block 3 Size: 300

Enter the number of processes: 4
Enter memory sizes required for the 4 processes:
Process 1 Size: 400
Process 2 Size: 100
Process 3 Size: 200
Process 4 Size: 300

Process No.	Process Size	Block No.	Fragment Size
-----------------------------------------------------------------
1		      400		     Not Allocated	  -
2		      100		       1		      0
3		      200		       2		      0
4		      300		       3		      0

```

<hr>

<p>16. Write a program to Implement type checking.</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

/* Simple memory buffers to track variable declarations */
char int_vars[20][20], float_vars[20][20];
int int_cnt = 0, float_cnt = 0;

void declare_var(char *name, int type);
int lookup_type(char *name);
void check_assignment(char *name, int val_type);
%}

ID      [a-zA-Z_][a-zA-Z0-9_]*
INT_VAL [0-9]+
FLT_VAL [0-9]+\.[0-9]+
WS      [ \t]*

%%

"int"{WS}{ID}{WS}"="{WS}{INT_VAL} {
    char name[20];
    sscanf(yytext, "int %[^= \t]", name);
    declare_var(name, 1);
    printf("Result: Type Check Successful for inline initialization of '%s'\n", name);
}

"int"{WS}{ID}{WS}"="{WS}{FLT_VAL} {
    char name[20];
    sscanf(yytext, "int %[^= \t]", name);
    printf("Error: Type Mismatch! Cannot initialize int variable '%s' with a float value\n", name);
}

"float"{WS}{ID}{WS}"="{WS}{FLT_VAL} {
    char name[20];
    sscanf(yytext, "float %[^= \t]", name);
    declare_var(name, 2);
    printf("Result: Type Check Successful for inline initialization of '%s'\n", name);
}

"float"{WS}{ID}{WS}"="{WS}{INT_VAL} {
    char name[20];
    sscanf(yytext, "float %[^= \t]", name);
    printf("Error: Type Mismatch! Cannot initialize float variable '%s' with an int value\n", name);
}

"int"{WS}{ID} {
    char name[20];
    sscanf(yytext, "int %s", name);
    declare_var(name, 1);
}

"float"{WS}{ID} {
    char name[20];
    sscanf(yytext, "float %s", name);
    declare_var(name, 2);
}

{ID}{WS}"="{WS}{INT_VAL} {
    char name[20];
    sscanf(yytext, "%[^= \t]", name);
    check_assignment(name, 1);
}

{ID}{WS}"="{WS}{FLT_VAL} {
    char name[20];
    sscanf(yytext, "%[^= \t]", name);
    check_assignment(name, 2);
}

{ID}{WS}"="{WS}{ID} {
    char name1[20], name2[20];
    sscanf(yytext, "%[^= \t] = %s", name1, name2);
    
    int type2 = lookup_type(name2);
    if(type2 != 0) {
        check_assignment(name1, type2);
    }
}

[\n\t ;] { ; }
.        { ; }

%%

void declare_var(char *name, int type) {
    if (type == 1) strcpy(int_vars[int_cnt++], name);
    else if (type == 2) strcpy(float_vars[float_cnt++], name);
}

int lookup_type(char *name) {
    for(int i = 0; i < int_cnt; i++) {
        if(strcmp(int_vars[i], name) == 0) return 1;
    }
    for(int i = 0; i < float_cnt; i++) {
        if(strcmp(float_vars[i], name) == 0) return 2;
    }
    printf("Error: Variable '%s' is undeclared\n", name);
    return 0;
}

void check_assignment(char *name, int val_type) {
    int id_type = lookup_type(name);
    if (id_type == 0) return;

    if (id_type == val_type) {
        printf("Result: Type Check Successful for assignment to '%s'\n", name);
    } else {
        printf("Error: Type Mismatch! Cannot assign %s value to '%s'\n", 
               (val_type == 1) ? "int" : "float", name);
    }
}

int yywrap() { return 1; }

int main() {
    printf("--- Semantic Type Checker ---\n");
    printf("Enter statements (e.g., int x = 5; or float y; y = 3.14;):\n\n");
    yylex();
    return 0;
}

```

### Compilation and Running Commands
```bash
usr@user:~$ flex lex.l
usr@user:~$ gcc lex.yy.c -o type_check
usr@user:~$ ./type_check
```
### Sample Output 
```text
--- Semantic Type Checker ---
Enter statements (e.g., int x = 5; or float y; y = 3.14;):

int x = 5;
Result: Type Check Successful for inline initialization of 'x'
float y;
y = 31.4;
Result: Type Check Successful for assignment to 'y'
x = y;
Error: Type Mismatch! Cannot assign float value to 'x'

```

<hr>

<p>17. Write a program to Implement Simple Code Optimization Techniques using Strength reduction</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <stdlib.h>

void reduce_mult(char *var, int num);
void reduce_pow(char *var1, char *var2, int num);
%}

%option noyywrap

/* Token Definitions */
ID    [a-zA-Z_][a-zA-Z0-9_]*
NUM   [0-9]+
WS    [ \t]*

%%

{ID}{WS}={WS}{ID}{WS}\*{WS}{NUM} {
    // Matches: x = x * 8
    char var1[20], var2[20];
    int val;
    sscanf(yytext, "%s = %s * %d", var1, var2, &val);
    reduce_mult(var1, val);
}

{ID}{WS}={WS}{ID}{WS}\^{WS}{NUM} {
    // Matches: x = y ^ 2
    char var1[20], var2[20];
    int val;
    sscanf(yytext, "%s = %s ^ %d", var1, var2, &val);
    reduce_pow(var1, var2, val);
}

\n      { putchar('\n'); }
.       { putchar(yytext[0]); }

%%

void reduce_mult(char *var, int num) {
    // Rule 1: Multiply by 2 -> Change to Addition
    if (num == 2) {
        printf("%s = %s + %s;", var, var, var);
    } 
    // Rule 2: Multiply by power of 2 -> Change to Left Shift (<<)
    else if ((num > 0) && (num & (num - 1)) == 0) {
        int shift = 0;
        while (num > 1) { shift++; num /= 2; }
        printf("%s = %s << %d;", var, var, shift);
    } 
    // Default: Leave it as is
    else {
        printf("%s = %s * %d;", var, var, num);
    }
}

void reduce_pow(char *var1, char *var2, int num) {
    // Rule 3: Squaring a number -> Change to Multiplication
    if (num == 2) {
        printf("%s = %s * %s;", var1, var2, var2);
    } else {
        printf("%s = pow(%s, %d);", var1, var2, num);
    }
}

int main() {
    printf("Enter statements (e.g., x = x * 8 or y = z ^ 2):\n");
    yylex();
    return 0;
}

```

### Compilation and Running Commands
```bash
usr@user:~$ flex lex.l
usr@user:~$ gcc lex.yy.c -o lexer
usr@user:~$ ./lexer
```
### Sample Output 
```text
Enter statements (e.g., x = x * 8 or y = z ^ 2):
x = x * 8
x = x << 3;

x = x + 1;
x = x + 1;

y = z ^ 2;
y = z * z;
```

<hr>

<p>18. Write a program to implement to implement the symbol table</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX 100

struct Entry {
    char name[20];
    char type[20];
    int line_no;
} symbolTable[MAX];

int count = 0;
int current_line = 1;
char current_type[20] = "unknown";

void insert(char *name, char *type, int line);
void display();
%}

%option noyywrap

/* Regular Expressions */
ALPHA   [a-zA-Z_]
DIGIT   [0-9]
ID      {ALPHA}({ALPHA}|{DIGIT})*
WS      [ \t]+

%%

"exit"      { display(); exit(0); } /* Typing exit prints table and stops program */
\n          { current_line++; }
"int"       { strcpy(current_type, "INT"); }
"float"     { strcpy(current_type, "FLOAT"); }
"char"      { strcpy(current_type, "CHAR"); }
";"         { strcpy(current_type, "unknown"); } 

"printf"    ; /* Ignore printf function name */
'[^'\n]*'   ; /* Ignore single-quoted strings like '%d' */
\"[^\"\n]*\" ; /* Ignore double-quoted strings */

{ID}        { 
    /* Inserts variable if inside declaration context or single letter vars */
    if (strcmp(current_type, "unknown") != 0 || yyleng == 1) { 
        insert(yytext, current_type, current_line); 
    }
}

{WS}        ; /* Ignore whitespace */
.           ; /* Ignore symbols */

%%

void insert(char *name, char *type, int line) {
    for (int i = 0; i < count; i++) {
        if (strcmp(symbolTable[i].name, name) == 0) {
            if (strcmp(symbolTable[i].type, "unknown") == 0 && strcmp(type, "unknown") != 0) {
                strcpy(symbolTable[i].type, type);
            }
            return; 
        }
    }
    
    strcpy(symbolTable[count].name, name);
    strcpy(symbolTable[count].type, type);
    symbolTable[count].line_no = line;
    count++;
}

void display() {
    printf("\n------------ SYMBOL TABLE ------------\n");
    printf("%-15s %-15s %-10s\n", "Identifier", "Type", "Line No.");
    printf("--------------------------------------\n");
    for (int i = 0; i < count; i++) {
        printf("%-15s %-15s %-10d\n", symbolTable[i].name, symbolTable[i].type, symbolTable[i].line_no);
    }
    printf("--------------------------------------\n");
}

int main() {
    printf("Enter code lines (Type 'exit' on a new line to view the table):\n");
    yylex();
    return 0;
}

```

### Compilation and Running Commands
```bash
usr@user:~$ flex lex.l
usr@user:~$ gcc lex.yy.c -o lexer
usr@user:~$ ./lexer
```
### Sample Output 
```text
Enter code lines (Type 'exit' on a new line to view the table):
int a;
int b;
print('%d', a);  
exit

------------ SYMBOL TABLE ------------
Identifier      Type            Line No.  
--------------------------------------
a               INT             1         
b               INT             2         
--------------------------------------

```

<hr>

<p>19. Write a program to Implement Simple Code Optimization Techniques using Algebraic
transformation</p>

### lex.l
```lex
%{
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

void trim_print(char *str) {
    while(isspace((unsigned char)*str)) str++;
    int len = strlen(str);
    while(len > 0 && isspace((unsigned char)str[len-1])) len--;
    printf("%.*s", len, str);
}
%}

/* Regular Definitions */
ID      [a-zA-Z_][a-zA-Z0-9_]*
NUM     [0-9]+
WS      [ \t]*

%%

{ID}{WS}={WS}{ID}{WS}\+{WS}0{WS}; {
    char lhs[50], rhs[50];
    sscanf(yytext, "%[^=]=%[^+]+0;", lhs, rhs);
    printf("\ninput: %s\n", yytext);
    printf("output: "); trim_print(lhs); printf(" = "); trim_print(rhs); printf(";\n");
}

{ID}{WS}={WS}0{WS}\+{WS}{ID}{WS}; {
    char lhs[50], rhs[50];
    sscanf(yytext, "%[^=]=0+%[^;];", lhs, rhs);
    printf("\ninput: %s\n", yytext);
    printf("output: "); trim_print(lhs); printf(" = "); trim_print(rhs); printf(";\n");
}

{ID}{WS}={WS}{ID}{WS}\*{WS}1{WS}; {
    char lhs[50], rhs[50];
    sscanf(yytext, "%[^=]=%[^*]*1;", lhs, rhs);
    printf("\ninput: %s\n", yytext);
    printf("output: "); trim_print(lhs); printf(" = "); trim_print(rhs); printf(";\n");
}

{ID}{WS}={WS}1{WS}\*{WS}{ID}{WS}; {
    char lhs[50], rhs[50];
    sscanf(yytext, "%[^=]=1*%[^;];", lhs, rhs);
    printf("\ninput: %s\n", yytext);
    printf("output: "); trim_print(lhs); printf(" = "); trim_print(rhs); printf(";\n");
}

{ID}{WS}={WS}{ID}{WS}\*{WS}0{WS}; {
    char lhs[50];
    sscanf(yytext, "%[^=]=", lhs);
    printf("\ninput: %s\n", yytext);
    printf("output: "); trim_print(lhs); printf(" = 0;\n");
}

\n      { /* Catch newline to avoid breaking formatting */ }
.       { /* Catch other characters */ }

%%

int yywrap() {
    return 1;
}

int main() {
    printf("----- Algebraic Optimization Program -----\n");
    printf("Enter statements (e.g., a = b + 0;) followed by Enter:\n");
    yylex();
    return 0;
}

```

### Compilation and Running Commands
```bash
usr@user:~$ flex lex.l
usr@user:~$ gcc lex.yy.c -o lexer
usr@user:~$ ./lexer
```
### Sample Output 
```text
----- Algebraic Optimization Program -----
Enter statements (e.g., a = b + 0;) followed by Enter:
a = a * 1;

input: a = a * 1;
output: a = a;

```

<hr>

<p>20. Write a program to generate three address code using LEX and YACC.</p>

### tac.l
```lex
%{
#include "y.tab.h"
#include <string.h>
#include <stdlib.h>
extern YYSTYPE yylval;
%}

%%
[a-zA-Z_][a-zA-Z0-9_]* { 
    yylval.str = strdup(yytext); 
    return ID; 
}
[0-9]+ { 
    yylval.str = strdup(yytext); 
    return NUM; 
}
[ \t]   { /* Ignore whitespace */ }
\n      { return 0; /* End of input on newline */ }
.       { return yytext[0]; }
%%

int yywrap() {
    return 1;
}

```
### tac.y
```yacc
%{
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void yyerror(const char *s);
int yylex(void);

int temp_count = 0;

char* create_temp() {
    char *str = (char*)malloc(10 * sizeof(char));
    sprintf(str, "t%d", temp_count++);
    return str;
}
%}

%union {
    char *str;
}

%token <str> ID NUM
%type <str> E

%left '+' '-'
%left '*' '/'

%%
  
S : ID '=' E ';' { 
        printf("%s = %s\n", $1, $3); 
    }
  ;

E : E '+' E { 
        $$ = create_temp(); 
        printf("%s = %s + %s\n", $$, $1, $3); 
    }
  | E '-' E { 
        $$ = create_temp(); 
        printf("%s = %s - %s\n", $$, $1, $3); 
    }
  | E '*' E { 
        $$ = create_temp(); 
        printf("%s = %s * %s\n", $$, $1, $3); 
    }
  | E '/' E { 
        $$ = create_temp(); 
        printf("%s = %s / %s\n", $$, $1, $3); 
    }
  | '(' E ')' { 
        $$ = $2; 
    }
  | ID { 
        $$ = $1; 
    }
  | NUM { 
        $$ = $1; 
    }
  ;

%%

void yyerror(const char *s) {
    fprintf(stderr, "Error: %s\n", s);
}

int main() {
    printf("----- Three Address Code Generator -----\n");
    printf("Enter an assignment statement (e.g., x = a + b * c;):\n");
    yyparse();
    return 0;
}

```

### Compilation and Running Commands
```bash
usr@user:~$ yacc -d tac.y
usr@user:~$ flex tac.l
usr@user:~$ gcc y.tab.c lex.yy.c -o tac
usr@user:~$ ./tac
```
### Sample Output 
```text
----- Three Address Code Generator -----
Enter an assignment statement (e.g., x = a + b * c;):
x = a + b * c;
t0 = b * c
t1 = a + t0
x = t1

```

<hr>





