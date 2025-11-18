```
#include <stdio.h>
#include <string.h>

#define MAX 10

char productions[MAX][30];
char nonterm[MAX];
char first[MAX][30];
int n, nt;
int visited[MAX];

int isNonTerminal(char c) {
    return (c >= 'A' && c <= 'Z');
}

void addToFirst(int idx, char c) {
    int len = strlen(first[idx]);
    for (int i = 0; i < len; i++)
        if (first[idx][i] == c) return;
    first[idx][len] = c;
    first[idx][len + 1] = '\0';
}

void computeFirst(int idx) {
    char X = nonterm[idx];
    if (visited[idx]) return;
    visited[idx] = 1;

    for (int p = 0; p < n; p++) {
        if (productions[p][0] != X) continue;

        int len = strlen(productions[p]);
        for (int k = 3; k < len; k++) {
            char c = productions[p][k];

            if (!isNonTerminal(c)) {
                addToFirst(idx, c);
                break;
            }

            int ntIndex = -1;
            for (int j = 0; j < nt; j++)
                if (nonterm[j] == c) {
                    ntIndex = j;
                    break;
                }

            if (ntIndex != -1) {
                computeFirst(ntIndex);
                for (int j = 0; j < strlen(first[ntIndex]); j++)
                    addToFirst(idx, first[ntIndex][j]);
                break;
            }
        }
    }
}

int main() {
    printf("=== FIRST Function Calculator ===\n\n");

    printf("Enter number of productions: ");
    scanf("%d", &n);
    getchar();  // consume newline

    printf("Enter productions (format: A->xyz):\n");
    for (int i = 0; i < n; i++) {
        printf("Production %d: ", i + 1);
        fgets(productions[i], 30, stdin);
        // Remove trailing newline
        int len = strlen(productions[i]);
        if (productions[i][len - 1] == '\n')
            productions[i][len - 1] = '\0';
    }

    printf("\nEnter number of non-terminals: ");
    scanf("%d", &nt);
    getchar();

    printf("Enter non-terminals (one per line):\n");
    for (int i = 0; i < nt; i++) {
        printf("Non-terminal %d: ", i + 1);
        char input[10];
        fgets(input, 10, stdin);
        nonterm[i] = input[0];  // Take first character
    }

    for (int i = 0; i < nt; i++) {
        first[i][0] = '\0';
        visited[i] = 0;
    }

    printf("\n=== Computing FIRST Sets ===\n");
    for (int i = 0; i < nt; i++) {
        visited[i] = 0;
        computeFirst(i);
    }

    printf("\n=== FIRST Sets ===\n");
    for (int i = 0; i < nt; i++) {
        printf("FIRST(%c) = { ", nonterm[i]);
        for (int j = 0; j < strlen(first[i]); j++) {
            printf("%c", first[i][j]);
            if (j < strlen(first[i]) - 1) printf(", ");
        }
        printf(" }\n");
    }

    return 0;
}
```
