
#include <stdio.h>
#include <stdlib.h>

#define MAX 5

int queue[MAX];
int front = -1;
int rear = -1;

// Function to add a job to the queue
void addJob()
{
    int job;

    if (rear == MAX - 1)
    {
        printf("\nQueue Overflow! Cannot add more jobs.\n");
        return;
    }

    printf("Enter Job ID: ");
    scanf("%d", &job);

    if (front == -1)
    {
        front = 0;
    }

       rear++;
       queue[rear] = job;

        printf("Job %d added successfully.\n", job);
}

// Function to delete/process a job
 void deleteJob()
{
    if (front == -1 || front > rear)
    {
        printf("\nQueue Underflow! No jobs to process.\n");
        return;
    }

    printf("\nJob %d processed and deleted successfully.\n",
           queue[front]);

    front++;

    if   (front > rear)
    {
        front = -1;
        rear = -1;
    }
}

// Function to display pending jobs
void displayJobs()
{
    int i;

    if (front == -1)
    {
           printf("\nNo pending jobs in the queue.\n");
        return;
    }

    printf("\nPending jobs (Front to Rear):\n");

    for (i = front; i <= rear; i++)
    {
           printf("%d ", queue[i]);
    }

       printf("\n");
}

int main()
{
    int choice;

    while (1)
    {
        printf("\n===== JOB QUEUE SIMULATION =====\n");
        printf("1. Add Job\n");
          printf("2. Delete/Process Job\n");
        printf("3. Display Pending Jobs\n");
        printf("4. Exit\n");
          printf("Enter your choice: ");

        if (scanf("%d", &choice) != 1)
        {
            printf("Invalid input. Exiting program.\n");
            break;
        }

        switch (choice)
        {
    case 1:
                addJob();
                break;

            case 2:
                deleteJob();
                break;

            case 3:
                displayJobs();
                break;

    case 4:
                printf("\nExiting Job Queue Simulation.\n");
                exit(0);

    default:
                printf("\nInvalid choice! Please try again.\n");
        }
    }

    return 0;
}
