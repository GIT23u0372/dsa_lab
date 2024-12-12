# dsa_lab
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>
#include <string.h>

#define MAX 100

// Stack structure
typedef struct {
    char arr[MAX];
    int top;
} Stack;

// Function to initialize the stack
void initStack(Stack* s) {
    s->top = -1;
}

// Function to check if the stack is empty
int isEmpty(Stack* s) {
    return s->top == -1;
}

// Function to check if the stack is full
int isFull(Stack* s) {
    return s->top == MAX - 1;
}

// Function to push an element onto the stack
void push(Stack* s, char val) {
    if (isFull(s)) {
        printf("Stack Overflow\n");
        return;
    }
    s->arr[++(s->top)] = val;
}

// Function to pop an element from the stack
char pop(Stack* s) {
    if (isEmpty(s)) {
        printf("Stack Underflow\n");
        return '\0';
    }
    return s->arr[(s->top)--];
}

// Function to peek the top element of the stack
char peek(Stack* s) {
    if (isEmpty(s)) {
        return '\0';
    }
    return s->arr[s->top];
}

// Function to check if a character is an operator
int isOperator(char ch) {
    return ch == '+' || ch == '-' || ch == '*' || ch == '/';
}

// Function to determine operator precedence
int precedence(char op) {
    switch (op) {
        case '*':
        case '/': return 2;
        case '+':
        case '-': return 1;
        default: return 0;
    }
}

// Function to check if parentheses in the expression are balanced
int areParenthesesBalanced(const char* exp) {
    Stack stack;
    initStack(&stack);

    for (int i = 0; exp[i] != '\0'; i++) {
        if (exp[i] == '(') {
            push(&stack, '(');
        } else if (exp[i] == ')') {
            if (isEmpty(&stack)) {
                return 0; // Unbalanced
            }
            pop(&stack);
        }
    }
    return isEmpty(&stack);
}

// Function to convert infix expression to postfix
void infixToPostfix(const char* infix, char* postfix) {
    Stack stack;
    initStack(&stack);
    int j = 0;

    for (int i = 0; infix[i] != '\0'; i++) {
        char ch = infix[i];

        // If operand, add to postfix
        if (isalnum(ch)) {
            postfix[j++] = ch;
        }
        // If '(', push to stack
        else if (ch == '(') {
            push(&stack, ch);
        }
        // If ')', pop and add to postfix until '(' is found
        else if (ch == ')') {
            while (!isEmpty(&stack) && peek(&stack) != '(') {
                postfix[j++] = pop(&stack);
            }
            pop(&stack); // Remove '('
        }
        // If operator, pop higher or equal precedence operators and push current operator
        else if (isOperator(ch)) {
            while (!isEmpty(&stack) && precedence(peek(&stack)) >= precedence(ch)) {
                postfix[j++] = pop(&stack);
            }
            push(&stack, ch);
        }
    }

    // Pop remaining operators
    while (!isEmpty(&stack)) {
        postfix[j++] = pop(&stack);
    }

    postfix[j] = '\0'; // Null-terminate the postfix expression
}

int main() {
    char infix[MAX], postfix[MAX];

    printf("Enter an infix expression: ");
    scanf("%s", infix);

    if (!areParenthesesBalanced(infix)) {
        printf("Invalid expression: Unbalanced parentheses\n");
        return 1;
    }

    infixToPostfix(infix, postfix);

    printf("Postfix expression: %s\n", postfix);

    return 0;
}
