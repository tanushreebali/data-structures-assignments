
#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define SIZE 100

// PUSH ELEMENT INTO STACK
void push(int *top, int stack[], int element)
{
    if (*top == SIZE - 1)
    {
        printf("Stack Overflow.\n");
        return;
    }

    stack[++(*top)] = element;
}

// POP ELEMENT FROM STACK
int pop(int *top, int stack[])
{
    if (*top == -1)
    {
        printf("Stack Underflow.\n");
        return 0;
    }

    return stack[(*top)--];
}

// EVALUATE POSTFIX EXPRESSION
void evaluatePostfix(char postfix[])
{
    int stack[SIZE];
    int top = -1;
    int i = 0;
    int a, b, result;

    while (postfix[i] != '\0')
    {
        // IGNORE SPACES
        if (isspace((unsigned char)postfix[i]))
        {
            i++;
            continue;
        }

        // OPERAND
        if (isdigit((unsigned char)postfix[i]))
        {
            push(&top, stack, postfix[i] - '0');
        }

        // OPERATOR
        else if (postfix[i] == '+' || postfix[i] == '-' ||
                 postfix[i] == '*' || postfix[i] == '/')
        {
            if (top < 1)
            {
                printf("Invalid postfix expression.\n");
                return;
            }

            b = pop(&top, stack);
            a = pop(&top, stack);

            switch (postfix[i])
            {
                case '+':
                    result = a + b;
                    break;

                case '-':
                    result = a - b;
                    break;

                case '*':
                    result = a * b;
                    break;

                case '/':
                    if (b == 0)
                    {
                        printf("Division by zero error.\n");
                        return;
                    }
                    result = a / b;
                    break;
            }

            push(&top, stack, result);
        }
        else
        {
            printf("Invalid character in expression.\n");
            return;
        }

        i++;
    }

    if (top != 0)
    {
        printf("Invalid postfix expression.\n");
        return;
    }

    printf("Result = %d\n", pop(&top, stack));
}

int main()
{
    char postfix[SIZE];

    printf("Enter postfix expression: ");
    fgets(postfix, SIZE, stdin);

    postfix[strcspn(postfix, "\n")] = '\0';

    evaluatePostfix(postfix);

    return 0;
}
