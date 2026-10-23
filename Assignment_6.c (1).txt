
#include <stdio.h>
#include <stdlib.h>

struct PolynomialTerm {
    int coefficient;
       int exponent;
    struct PolynomialTerm *next;
};

typedef struct PolynomialTerm PolynomialTerm;

PolynomialTerm *insertTerm(PolynomialTerm *polyHead, int termCoefficient, int termExponent);
   PolynomialTerm *createPolynomial(void);
void displayPolynomial(PolynomialTerm *polyHead);
PolynomialTerm *addPolynomials(PolynomialTerm *firstPoly, PolynomialTerm *secondPoly);
void freePolynomial(PolynomialTerm *polyHead);

PolynomialTerm *insertTerm(PolynomialTerm *polyHead, int termCoefficient, int termExponent)
{
    if (termCoefficient == 0)
        return polyHead;

    PolynomialTerm *newTerm = malloc(sizeof(PolynomialTerm));

    if (newTerm == NULL) {
        printf("Memory allocation failed.\n");
        exit(EXIT_FAILURE);
    }

    newTerm->coefficient = termCoefficient;
    newTerm->exponent = termExponent;
    newTerm->next = NULL;

    if (polyHead == NULL) {
        newTerm->next = newTerm;
        return newTerm;
    }

    if (termExponent == polyHead->exponent) {
        polyHead->coefficient += termCoefficient;
        free(newTerm);

        if (polyHead->coefficient != 0)
            return polyHead;

        PolynomialTerm *lastTerm = polyHead;

        while (lastTerm->next != polyHead)
            lastTerm = lastTerm->next;

        if (lastTerm == polyHead) {
            free(polyHead);
            return NULL;
        }

        PolynomialTerm *nextHead = polyHead->next;
        lastTerm->next = nextHead;
        free(polyHead);

        return nextHead;
    }

    if (termExponent > polyHead->exponent) {
        PolynomialTerm *lastTerm = polyHead;

        while (lastTerm->next != polyHead)
            lastTerm = lastTerm->next;

        newTerm->next = polyHead;
        lastTerm->next = newTerm;

        return newTerm;
    }

    PolynomialTerm *previousTerm = polyHead;
    PolynomialTerm *currentTerm = polyHead->next;

    while (currentTerm != polyHead &&
           currentTerm->exponent > termExponent) {
        previousTerm = currentTerm;
        currentTerm = currentTerm->next;
    }

    if (currentTerm != polyHead &&
        currentTerm->exponent == termExponent) {
        currentTerm->coefficient += termCoefficient;
        free(newTerm);

        if (currentTerm->coefficient == 0) {
            previousTerm->next = currentTerm->next;
            free(currentTerm);
        }

        return polyHead;
    }

    newTerm->next = currentTerm;
    previousTerm->next = newTerm;

    return polyHead;
}

PolynomialTerm *createPolynomial(void)
{
    PolynomialTerm *polyHead = NULL;
       int totalTerms;
    int termCoefficient;
    int termExponent;

    printf("Enter the number of terms: ");
    if (scanf("%d", &totalTerms) != 1 || totalTerms < 0) {
        printf("Invalid number of terms.\n");
        exit(EXIT_FAILURE);
    }

    for (int termIndex = 0; termIndex < totalTerms; termIndex++) {
          printf("Enter coefficient and exponent for term %d: ",
               termIndex + 1);

        if (scanf("%d %d", &termCoefficient, &termExponent) != 2 ||
            termExponent < 0) {
            printf("Invalid polynomial term.\n");
            freePolynomial(polyHead);
            exit(EXIT_FAILURE);
        }

        polyHead = insertTerm(polyHead, termCoefficient, termExponent);
    }

    return polyHead;
}

void displayPolynomial(PolynomialTerm *polyHead)
{
    if (polyHead == NULL) {
        printf("0\n");
        return;
    }

    PolynomialTerm *currentTerm = polyHead;
    int firstTerm = 1;

    do {
          int termCoefficient = currentTerm->coefficient;
        int termExponent = currentTerm->exponent;

        if (termCoefficient != 0) {
            if (firstTerm) {
                if (termCoefficient < 0)
                    printf("-");
            } else {
                printf(termCoefficient < 0 ? " - " : " + ");
            }

            int absoluteCoefficient =
                termCoefficient < 0 ? -termCoefficient : termCoefficient;

            if (termExponent == 0) {
                printf("%d", absoluteCoefficient);
            } else {
                if (absoluteCoefficient != 1)
                    printf("%d", absoluteCoefficient);

                printf("x");

                if (termExponent != 1)
                    printf("^%d", termExponent);
            }

            firstTerm = 0;
        }

          currentTerm = currentTerm->next;

    } while (currentTerm != polyHead);

    if (firstTerm)
        printf("0");

    printf("\n");
}

PolynomialTerm *addPolynomials(PolynomialTerm *firstPoly,
                               PolynomialTerm *secondPoly)
{
    PolynomialTerm *resultPoly = NULL;

    if (firstPoly != NULL) {
        PolynomialTerm *firstCursor = firstPoly;

        do {
            resultPoly = insertTerm(resultPoly,
                                    firstCursor->coefficient,
                                    firstCursor->exponent);

            firstCursor = firstCursor->next;

        } while (firstCursor != firstPoly);
    }

    if (secondPoly != NULL) {
        PolynomialTerm *secondCursor = secondPoly;

        do {
            resultPoly = insertTerm(resultPoly,
                                    secondCursor->coefficient,
                                    secondCursor->exponent);

            secondCursor = secondCursor->next;

        } while (secondCursor != secondPoly);
    }

    return resultPoly;
}

void freePolynomial(PolynomialTerm *polyHead)
{
    if (polyHead == NULL)
        return;
 
     PolynomialTerm *currentTerm = polyHead->next;

    while (currentTerm != polyHead) {
        PolynomialTerm *nextTerm = currentTerm->next;
        free(currentTerm);
        currentTerm = nextTerm;
    }

    free(polyHead);
}

int main(void)
{
    PolynomialTerm *firstPolynomial;
    PolynomialTerm *secondPolynomial;
      PolynomialTerm *resultPolynomial;

    printf("Enter the first polynomial:\n");
    firstPolynomial = createPolynomial();

     printf("\nEnter the second polynomial:\n");
    secondPolynomial = createPolynomial();

    printf("\nFirst Polynomial: ");
     displayPolynomial(firstPolynomial);

    printf("Second Polynomial: ");
    displayPolynomial(secondPolynomial);

    resultPolynomial = addPolynomials(firstPolynomial, secondPolynomial);

    printf("Resultant Polynomial: ");
    displayPolynomial(resultPolynomial);

    freePolynomial(firstPolynomial);
      freePolynomial(secondPolynomial);
    freePolynomial(resultPolynomial);

    return 0;
}
