# Compiler Design Practicals
### Experiment 1

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
