# <stdio.h>
#include <string.h>
#include <ctype.h>
#include <stdlib.h>

#define MAX 500

// ------------------------------------
// Step 1: Lexical Analysis
// ------------------------------------
void lexicalAnalyzer(char* code) {
    printf("\n--- Step 1: Lexical Analysis ---\n");
    printf("Lexical Analysis for the code: %s\n", code);
    for (int i = 0; code[i] != '\0'; i++) {
        if (isalpha(code[i])) {
            printf("Token: %c (Identifier)\n", code[i]);
        } else if (isdigit(code[i])) {
            printf("Token: %c (Number)\n", code[i]);
        } else if (code[i] == '+' || code[i] == '-' || code[i] == '*' || code[i] == '/') {
            printf("Token: %c (Operator)\n", code[i]);
        } else if (code[i] == ' ' || code[i] == '\t') {
            continue;
        } else {
            printf("Token: %c (Unknown)\n", code[i]);
        }
    }
}

// ------------------------------------
// Step 2: Syntax Analysis (Parsing)
// ------------------------------------
void syntaxAnalysis(char* code) {
    printf("\n--- Step 2: Syntax Analysis ---\n");

    int balance = 0;
    int hasOperator = 0;
    printf("| Character | Symbol Type |\n");
    printf("|-----------|-------------|\n");

    for (int i = 0; code[i] != '\0'; i++) {
        if (code[i] == '(') balance++;
        else if (code[i] == ')') balance--;

        if (code[i] == '+' || code[i] == '-' || code[i] == '*' || code[i] == '/') {
            hasOperator = 1;
        }

        if (isalpha(code[i])) {
            printf("| %c         | Identifier  |\n", code[i]);
        } else if (isdigit(code[i])) {
            printf("| %c         | Number      |\n", code[i]);
        } else if (code[i] == '+' || code[i] == '-' || code[i] == '*' || code[i] == '/') {
            printf("| %c         | Operator    |\n", code[i]);
        } else if (code[i] == '(' || code[i] == ')') {
            printf("| %c         | Parenthesis |\n", code[i]);
        } else {
            printf("| %c         | Unknown     |\n", code[i]);
        }
    }

    if (balance == 0) {
        printf("✓ Parentheses are balanced.\n");
    } else {
        printf("✗ Parentheses are NOT balanced.\n");
    }

    if (hasOperator) {
        printf("✓ Operators found: Syntax seems valid.\n");
    } else {
        printf("✗ No operator found: Might be a single operand or incomplete syntax.\n");
    }
}

// ------------------------------------
// Step 3: Semantic Analysis
// ------------------------------------
void semanticAnalysis(char* code) {
    printf("\n--- Step 3: Semantic Analysis ---\n");

    printf("| Rule                            | Status         |\n");
    printf("|----------------------------------|----------------|\n");

    if (strstr(code, "int") || strstr(code, "float") || strstr(code, "char")) {
        printf("| Data type declaration found     | ✓ Valid        |\n");
    } else {
        printf("| Data type declaration missing   | ✗ Warning      |\n");
    }

    if ((strstr(code, "int") || strstr(code, "float")) &&
        (strchr(code, '+') || strchr(code, '-') || strchr(code, '*') || strchr(code, '/'))) {
        printf("| Valid type and operation        | ✓ Valid        |\n");
    } else {
        printf("| Type mismatch or missing operation | ✗ Warning   |\n");
    }

    if (strchr(code, ';') == NULL) {
        printf("| Missing semicolon               | ✗ Warning      |\n");
    } else {
        printf("| Semicolon present               | ✓ Valid        |\n");
    }
}

// ------------------------------------
// Step 4: Intermediate Code Generation
// ------------------------------------
void intermediateCodeGeneration(char* code) {
    printf("\n--- Step 4: Intermediate Code Generation ---\n");

    char operators[MAX];
    char operands[MAX][MAX];
    int opIndex = -1, operandIndex = -1;
    int tempVarCounter = 1;

    void performOperation(char op) {
        char operand2[MAX], operand1[MAX];
        strcpy(operand2, operands[operandIndex--]);
        strcpy(operand1, operands[operandIndex--]);

        char tempVar[MAX];
        sprintf(tempVar, "t%d", tempVarCounter++);

        printf("Intermediate code: %s = %s %c %s\n", tempVar, operand1, op, operand2);

        operandIndex++;
        strcpy(operands[operandIndex], tempVar);
    }

    for (int i = 0; code[i] != '\0'; i++) {
        if (isspace(code[i])) continue;

        if (isalnum(code[i])) {
            char operand[2] = { code[i], '\0' };
            operandIndex++;
            strcpy(operands[operandIndex], operand);
        } else if (code[i] == '+' || code[i] == '-' || code[i] == '*' || code[i] == '/') {
            while (opIndex >= 0 && (operators[opIndex] == '*' || operators[opIndex] == '/') &&
                   (code[i] == '+' || code[i] == '-')) {
                performOperation(operators[opIndex--]);
            }
            operators[++opIndex] = code[i];
        } else if (code[i] == '(') {
            operators[++opIndex] = code[i];
        } else if (code[i] == ')') {
            while (operators[opIndex] != '(') {
                performOperation(operators[opIndex--]);
            }
            opIndex--;
        }
    }

    while (opIndex >= 0) {
        performOperation(operators[opIndex--]);
    }
}

// ------------------------------------
// Step 5: Code Optimization
// ------------------------------------
void codeOptimization(char* code) {
    printf("\n--- Step 5: Code Optimization ---\n");

    if (strstr(code, "int x") && !strstr(code, "x =")) {
        printf("Optimization: Removed unused variable 'x'\n");
    }

    if (strstr(code, "+ 0") || strstr(code, "0 +")) {
        printf("Optimization: Replaced 'a + 0' or '0 + a' with 'a'\n");
    }

    if (strstr(code, "* 1") || strstr(code, "1 *")) {
        printf("Optimization: Replaced 'a * 1' or '1 * a' with 'a'\n");
    }

    printf("Optimization complete.\n");
}

// ------------------------------------
// Step 6: Code Generation
// ------------------------------------
void codeGeneration(char* code) {
    printf("\n--- Step 6: Code Generation ---\n");

    if (strchr(code, '+') || strchr(code, '-') || strchr(code, '*') || strchr(code, '/')) {
        printf("Generated C code: printf(\"%%d\", %s);\n", code);
    } else {
        printf("Generated C code: printf(\"%%s\", \"%s\");\n", code);
    }
}

// ------------------------------------
// Step 7: Token Separator
// ------------------------------------
void separateTokens(char* input) {
    printf("\n--- Step 7: Token Separation ---\n");

    int count = 0;
    char *token = strtok(input, " ");
    while (token != NULL) {
        printf("Token %d: %s\n", ++count, token);
        token = strtok(NULL, " ");
    }
    printf("Total Tokens: %d\n", count);
}

// ------------------------------------
// Step 8: First and Follow Sets
// ------------------------------------
void findFirst(char grammar[10][10], int numRules) {
    printf("\n--- First Set ---\n");
    for (int i = 0; i < numRules; i++) {
        printf("First(%c) = {%c}\n", grammar[i][0], grammar[i][3]);
    }
}

void findFollow(char grammar[10][10], int numRules) {
    printf("\n--- Follow Set ---\n");
    for (int i = 0; i < numRules; i++) {
        if (i == 0)
            printf("Follow(%c) = {$}\n", grammar[i][0]);
        else
            printf("Follow(%c) = {%c}\n", grammar[i][0], grammar[i - 1][3]);
    }
}

// ------------------------------------
// Step 9: LL(1) Parsing (Simple)
// ------------------------------------
void LL1Parsing(char* code) {
    printf("\n--- LL(1) Parsing ---\n");
    char* token = strtok(code, " ");
    int i = 0;

    while (token != NULL) {
        if (i == 0 && strcmp(token, "int") == 0) {
            printf("Parse tree: E -> T -> int\n");
        } else if (i == 1 && strcmp(token, "+") == 0) {
            printf("Parse tree: T -> + E\n");
        } else if (i == 0) {
            printf("Error: Expected 'int' at beginning\n");
            return;
        } else {
            printf("Parse tree: E -> T\n");
        }
        i++;
        token = strtok(NULL, " ");
    }
}

// ------------------------------------
// Main Function with Menu
// ------------------------------------
int main() {
    int choice;
    char code[MAX], str[MAX];
    char grammar[10][10];
    int numRules;

    while (1) {
        printf("\n====== Mini Compiler Design Menu ======\n");
        printf("1. Lexical Analysis\n");
        printf("2. Syntax Analysis (Parsing)\n");
        printf("3. Semantic Analysis\n");
        printf("4. Intermediate Code Generation\n");
        printf("5. Code Optimization\n");
        printf("6. Code Generation\n");
        printf("7. Separate Tokens\n");
        printf("8. First and Follow Sets\n");
        printf("9. LL(1) Parsing\n");
        printf("10. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);
        getchar();

        switch (choice) {
            case 1:
                printf("Enter code for lexical analysis: ");
                fgets(code, sizeof(code), stdin);
                code[strcspn(code, "\n")] = 0;
                lexicalAnalyzer(code);
                break;
            case 2:
                printf("Enter code for syntax analysis: ");
                fgets(code, sizeof(code), stdin);
                code[strcspn(code, "\n")] = 0;
                syntaxAnalysis(code);
                break;
            case 3:
                printf("Enter code for semantic analysis: ");
                fgets(code, sizeof(code), stdin);
                code[strcspn(code, "\n")] = 0;
                semanticAnalysis(code);
                break;
            case 4:
                printf("Enter expression (e.g., a + b): ");
                fgets(code, sizeof(code), stdin);
                code[strcspn(code, "\n")] = 0;
                intermediateCodeGeneration(code);
                break;
            case 5:
                printf("Enter code for optimization: ");
                fgets(code, sizeof(code), stdin);
                code[strcspn(code, "\n")] = 0;
                codeOptimization(code);
                break;
            case 6:
                printf("Enter code for code generation: ");
                fgets(code, sizeof(code), stdin);
                code[strcspn(code, "\n")] = 0;
                codeGeneration(code);
                break;
            case 7:
                printf("Enter string to separate tokens: ");
                fgets(str, sizeof(str), stdin);
                str[strcspn(str, "\n")] = 0;
                separateTokens(str);
                break;
            case 8:
                printf("Enter number of grammar rules: ");
                scanf("%d", &numRules);
                getchar();
                for (int i = 0; i < numRules; i++) {
                    printf("Enter grammar rule %d (e.g., E->a): ", i + 1);
                    fgets(grammar[i], sizeof(grammar[i]), stdin);
                    grammar[i][strcspn(grammar[i], "\n")] = 0;
                }
                findFirst(grammar, numRules);
                findFollow(grammar, numRules);
                break;
            case 9:
                printf("Enter code for LL(1) parsing: ");
                fgets(str, sizeof(str), stdin);
                str[strcspn(str, "\n")] = 0;
                LL1Parsing(str);
                break;
            case 10:
                printf("Exiting Compiler...\n");
                return 0;
            default:
                printf("Invalid choice. Try again.\n");
        }
    }
}
