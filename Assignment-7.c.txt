
#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define SIZE 100

 // CHECK PRECEDENCE OF OPERATORS
int precedence(char ch)
{
    if (ch == '+' || ch == '-')
        return 1;

    if (ch == '*' || ch == '/')
        return 2;

    return 0;
}

// CHARACTER STACK: PUSH
void push(int *top, char stack[], char element)
{
    if (*top == SIZE - 1)
    {
        printf("Stack Overflow.\n");
        return;
    }

    stack[++(*top)] = element;
}

// CHARACTER STACK: POP
char pop(int *top, char stack[])
{
    if (*top == -1)
    {
        printf("Stack Underflow.\n");
        return '\0';
    }

    return stack[(*top)--];
}

// 1. INFIX TO POSTFIX
void infixToPostfix(char infix[], char postfix[])
{
    char stack[SIZE];
    int top = -1, i = 0, j = 0;
    char element;

    while (infix[i] != '\0')
    {
        if (isspace((unsigned char)infix[i]))
        {
            i++;
            continue;
        }

        // OPERAND
        if (isalnum((unsigned char)infix[i]))
        {
            postfix[j++] = infix[i];
        }

        // OPENING BRACKET
        else if (infix[i] == '(')
        {
            push(&top, stack, infix[i]);
        }

        // CLOSING BRACKET
        else if (infix[i] == ')')
        {
            while (top != -1 && stack[top] != '(')
            {
                postfix[j++] = pop(&top, stack);
            }

            if (top != -1)
                pop(&top, stack);
        }

        // OPERATOR
        else if (infix[i] == '+' || infix[i] == '-' ||
                 infix[i] == '*' || infix[i] == '/')
        {
            while (top != -1 &&
                   stack[top] != '(' &&
                   precedence(infix[i]) <= precedence(stack[top]))
            {
                element = pop(&top, stack);
                postfix[j++] = element;
            }

            push(&top, stack, infix[i]);
        }

        i++;
    }

    // POP REMAINING OPERATORS
    while (top != -1)
    {
        element = pop(&top, stack);

        if (element != '(')
            postfix[j++] = element;
    }

    postfix[j] = '\0';

    printf("Postfix expression: %s\n", postfix);
}

// STRING STACK: PUSH
void pushString(int *top, char stack[][SIZE], char str[])
{
    if (*top == SIZE - 1)
    {
        printf("Stack Overflow.\n");
        return;
    }

    strcpy(stack[++(*top)], str);
}

// STRING STACK: POP
void popString(int *top, char stack[][SIZE], char result[])
{
    if (*top == -1)
    {
        result[0] = '\0';
        return;
    }

    strcpy(result, stack[(*top)--]);
}

// 2. POSTFIX TO PREFIX
void postfixToPrefix(char postfix[], char prefix[])
{
    char stack[SIZE][SIZE];
    char left[SIZE], right[SIZE], temp[SIZE];
    int top = -1, i = 0;

    while (postfix[i] != '\0')
    {
        if (isspace((unsigned char)postfix[i]))
        {
            i++;
            continue;
        }

        // OPERAND
        if (isalnum((unsigned char)postfix[i]))
        {
            temp[0] = postfix[i];
            temp[1] = '\0';

            pushString(&top, stack, temp);
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

            popString(&top, stack, right);
            popString(&top, stack, left);

            snprintf(temp, SIZE, "%c%s%s",
                     postfix[i], left, right);

            pushString(&top, stack, temp);
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

    popString(&top, stack, prefix);

    printf("Prefix expression: %s\n", prefix);
}

// 3. PREFIX TO INFIX
void prefixToInfix(char prefix[], char infix[])
{
    char stack[SIZE][SIZE];
    char left[SIZE], right[SIZE], temp[SIZE];
    int top = -1;
    int i = (int)strlen(prefix) - 1;

    while (i >= 0)
    {
        if (isspace((unsigned char)prefix[i]))
        {
            i--;
            continue;
        }

        // OPERAND
        if (isalnum((unsigned char)prefix[i]))
        {
            temp[0] = prefix[i];
            temp[1] = '\0';

            pushString(&top, stack, temp);
        }

        // OPERATOR
        else if (prefix[i] == '+' || prefix[i] == '-' ||
                 prefix[i] == '*' || prefix[i] == '/')
        {
            if (top < 1)
            {
                printf("Invalid prefix expression.\n");
                return;
            }

            popString(&top, stack, left);
            popString(&top, stack, right);

            snprintf(temp, SIZE, "(%s%c%s",
                     left, prefix[i], right);

            // Add the closing parenthesis
            size_t len = strlen(temp);

            if (len + 1 >= SIZE)
            {
                printf("Expression too long.\n");
                return;
            }

            temp[len] = ')';
            temp[len + 1] = '\0';

            pushString(&top, stack, temp);
        }
        else
        {
            printf("Invalid character in expression.\n");
            return;
        }

        i--;
    }

    if (top != 0)
    {
        printf("Invalid prefix expression.\n");
        return;
    }

    popString(&top, stack, infix);

    printf("Infix expression: %s\n", infix);
}

// MAIN FUNCTION
int main()
{
    int choice;

    char infix[SIZE];
    char postfix[SIZE];
    char prefix[SIZE];

    printf("Enter your choice:\n");
    printf("1. Infix to Postfix\n");
    printf("2. Postfix to Prefix\n");
    printf("3. Prefix to Infix\n");
    printf("Choice: ");

    if (scanf("%d", &choice) != 1)
    {
        printf("Invalid input.\n");
        return 1;
    }

    getchar();  // Consume newline left by scanf

    switch (choice)
    {
        case 1:
            printf("Enter infix expression: ");
            fgets(infix, SIZE, stdin);
            infix[strcspn(infix, "\n")] = '\0';

            infixToPostfix(infix, postfix);
            break;

        case 2:
            printf("Enter postfix expression: ");
            fgets(postfix, SIZE, stdin);
            postfix[strcspn(postfix, "\n")] = '\0';

            postfixToPrefix(postfix, prefix);
            break;

        case 3:
            printf("Enter prefix expression: ");
            fgets(prefix, SIZE, stdin);
            prefix[strcspn(prefix, "\n")] = '\0';

            prefixToInfix(prefix, infix);
            break;

        default:
            printf("Invalid option!\n");
    }

    return 0;
}
