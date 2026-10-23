#include <stdio.h>
#include <string.h>

struct Student {
    char name[30];
    int roll;
    char division;
    float percentage;
    char city[10];
};

void inputData(int n, struct Student s[n]);
void linearSearch(int target, int n, struct Student s[n]);
void Nbinary(int n, struct Student s[n]);
int Rbinary(int low, int high, int key, struct Student s[]);
void insertionSort(int n, struct Student s[n]);
void selectionSort(int n, struct Student s[n]);
void shellSort(int n, struct Student s[n]);
void displayData(int i, int n, struct Student s[n]);

int main() {

    int n;

    printf("Enter the number of students: ");
    scanf("%d", &n);

    struct Student s[n];

    inputData(n, s);

    int choice, target;

    printf("\n===== Choose Searching Method =====\n");
    printf("1) Linear Search\n");
    printf("2) Non-Recursive Binary Search\n");
    printf("3) Recursive Binary Search\n");
    printf("Enter your choice: ");
    scanf("%d", &choice);

    switch (choice) {

        case 1:

            printf("\nEnter the roll number: ");
            scanf("%d", &target);

            linearSearch(target, n, s);

            break;

        case 2: {

            int choice1;

            printf("\n===== Choose Sorting Method =====\n");
            printf("1) Insertion Sort\n");
            printf("2) Selection Sort\n");
            printf("3) Shell Sort\n");
            printf("Enter your choice: ");
            scanf("%d", &choice1);

            switch (choice1) {

                case 1:
                    insertionSort(n, s);
                    break;

                case 2:
                    selectionSort(n, s);
                    break;

                case 3:
                    shellSort(n, s);
                    break;

                default:
                    printf("Invalid Sorting Method!\n");
            }

            Nbinary(n, s);

            break;
        }

        case 3: {

            int choice1;

            printf("\n===== Choose Sorting Method =====\n");
            printf("1) Insertion Sort\n");
            printf("2) Selection Sort\n");
            printf("3) Shell Sort\n");
            printf("Enter your choice: ");
            scanf("%d", &choice1);

            switch (choice1) {

                case 1:
                    insertionSort(n, s);
                    break;

                case 2:
                    selectionSort(n, s);
                    break;

                case 3:
                    shellSort(n, s);
                    break;

                default:
                    printf("Invalid Sorting Method!\n");
            }

            int key;

            printf("\nEnter the target roll number: ");
            scanf("%d", &key);

            int result = Rbinary(0, n - 1, key, s);

            if (result != -1) {
                printf("\nStudent Found!\n");
                displayData(result, n, s);
            }
            else {
                printf("\nStudent Not Found!\n");
            }

            break;
        }

        default:
            printf("Invalid Searching Method!\n");
    }

    return 0;
}


void inputData(int n, struct Student s[n]) {

    int count = 1;

    for (int i = 0; i < n; i++) {

        printf("\nEnter Data for Student-%d\n\n", count);

        printf("Enter the name of the student: ");

        getchar();

        fgets(s[i].name, sizeof(s[i].name), stdin);

        s[i].name[strcspn(s[i].name, "\n")] = '\0';

        printf("Enter the roll number: ");
        scanf("%d", &s[i].roll);

        printf("Enter the division: ");
        scanf(" %c", &s[i].division);

        printf("Enter the percentage: ");
        scanf("%f", &s[i].percentage);

        printf("Enter the city: ");
        scanf("%9s", s[i].city);

        count++;
    }
}


void linearSearch(int target, int n, struct Student s[n]) {

    for (int i = 0; i < n; i++) {

        if (target == s[i].roll) {

            printf("\nStudent Found!\n");
            displayData(i, n, s);

            return;
        }
    }

    printf("\nStudent Not Found!\n");
}


void Nbinary(int n, struct Student s[n]) {

    int key;

    printf("\nEnter the target roll number: ");
    scanf("%d", &key);

    int low = 0;
    int high = n - 1;
    int mid;

    while (low <= high) {

        mid = (low + high) / 2;

        if (s[mid].roll == key) {

            printf("\nStudent Found!\n");
            displayData(mid, n, s);

            return;
        }

        else if (key > s[mid].roll) {
            low = mid + 1;
        }

        else {
            high = mid - 1;
        }
    }

    printf("\nStudent Not Found!\n");
}


int Rbinary(int low, int high, int key, struct Student s[]) {

    if (low > high) {
        return -1;
    }

    int mid = (low + high) / 2;

    if (s[mid].roll == key) {
        return mid;
    }

    else if (key > s[mid].roll) {
        return Rbinary(mid + 1, high, key, s);
    }

    else {
        return Rbinary(low, mid - 1, key, s);
    }
}


void insertionSort(int n, struct Student s[n]) {

    int i, j;
    struct Student temp;

    for (i = 1; i < n; i++) {

        temp = s[i];
        j = i - 1;

        while (j >= 0 && s[j].roll > temp.roll) {

            s[j + 1] = s[j];
            j--;
        }

        s[j + 1] = temp;
    }
}


void selectionSort(int n, struct Student s[n]) {

    int i, j, min;
    struct Student temp;

    for (i = 0; i < n - 1; i++) {

        min = i;

        for (j = i + 1; j < n; j++) {

            if (s[j].roll < s[min].roll) {
                min = j;
            }
        }

        temp = s[i];
        s[i] = s[min];
        s[min] = temp;
    }
}


void shellSort(int n, struct Student s[n]) {

    int gap, i, j;
    struct Student temp;

    for (gap = n / 2; gap > 0; gap /= 2) {

        for (i = gap; i < n; i++) {

            temp = s[i];

            for (j = i; j >= gap && s[j - gap].roll > temp.roll;
                 j -= gap) {

                s[j] = s[j - gap];
            }

            s[j] = temp;
        }
    }
}


void displayData(int i, int n, struct Student s[n]) {

    printf("\nName       : %s", s[i].name);
    printf("\nRoll Number: %d", s[i].roll);
    printf("\nDivision   : %c", s[i].division);
    printf("\nPercentage : %.2f", s[i].percentage);
    printf("\nCity       : %s\n", s[i].city);
}