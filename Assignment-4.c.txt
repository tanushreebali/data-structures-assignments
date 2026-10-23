#include <stdio.h>
#include <stdlib.h>

void insertionSort(float bucket[], int size)
{
    int i, j;
    float key;

    for (i = 1; i < size; i++)
    {
        key = bucket[i];
        j = i - 1;

        while (j >= 0 && bucket[j] > key)
        {
            bucket[j + 1] = bucket[j];
            j--;
        }

        bucket[j + 1] = key;
    }
}

void bucketSort(float arr[], int n)
{
    int i, j;
    int index;

    // Create n buckets
    float **bucket = (float **)malloc(n * sizeof(float *));
    int *count = (int *)calloc(n, sizeof(int));

    for (i = 0; i < n; i++)
    {
        bucket[i] = (float *)malloc(n * sizeof(float));
    }

    // Distribute elements into buckets
    for (i = 0; i < n; i++)
    {
        index = (int)(arr[i] * n / 100.0);

        // Handle percentage = 100
        if (index >= n)
            index = n - 1;

        bucket[index][count[index]] = arr[i];
        count[index]++;
    }

    // Sort individual buckets
    for (i = 0; i < n; i++)
    {
        insertionSort(bucket[i], count[i]);
    }

    // Merge buckets back into array
    int k = 0;

    for (i = 0; i < n; i++)
    {
        for (j = 0; j < count[i]; j++)
        {
            arr[k++] = bucket[i][j];
        }
    }

    // Free memory
    for (i = 0; i < n; i++)
    {
        free(bucket[i]);
    }

    free(bucket);
    free(count);
}

void displayTopFive(float arr[], int n)
{
    int i;
    int limit = (n < 5) ? n : 5;

    printf("\nTop %d Scores:\n", limit);

    for (i = n - 1; i >= n - limit; i--)
    {
        printf("%.2f%%\n", arr[i]);
    }
}

int main()
{
    int n, i;
    float *percentage;

    printf("Enter number of students: ");
    scanf("%d", &n);

    if (n <= 0)
    {
        printf("Invalid number of students.\n");
        return 0;
    }

    percentage = (float *)malloc(n * sizeof(float));

    printf("\nEnter student percentages:\n");

    for (i = 0; i < n; i++)
    {
        do
        {
            printf("Student %d: ", i + 1);
            scanf("%f", &percentage[i]);

            if (percentage[i] < 0 || percentage[i] > 100)
                printf("Percentage must be between 0 and 100.\n");

        } while (percentage[i] < 0 || percentage[i] > 100);
    }

    // Apply Bucket Sort
    bucketSort(percentage, n);

    // Display sorted percentages
    printf("\nPercentages in Ascending Order:\n");

    for (i = 0; i < n; i++)
    {
        printf("%.2f%% ", percentage[i]);
    }

    // Display top five
    displayTopFive(percentage, n);

    free(percentage);

    return 0;
}