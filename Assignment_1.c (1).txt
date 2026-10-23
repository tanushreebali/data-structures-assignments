#include<stdio.h> 





//  Function Prototypes;

void inputData(int rows1,int cols1,int rows2,int cols2,int A[rows1][cols1],int B[rows2][cols2]);

void display(int rows,int cols,int C[rows][cols]);

void add(int rows1,int cols1,int rows2,int cols2,int A[rows1][cols1],int B[rows2][cols2]);

void sub(int rows1,int cols1,int rows2,int cols2,int A[rows1][cols1],int B[rows2][cols2]);

void multiply(int rows1,int cols1,int rows2,int cols2,int A[rows1][cols1],int B[rows2][cols2]);

void trans(int rows1,int cols1,int rows2,int cols2,int A[rows1][cols1],int B[rows2][cols2]);



int main(){

    // 1) Taking input of the data:

    int rows1,cols1,rows2,cols2;

    printf("Enter the number of rows for matrix-A:");
    scanf("%d",&rows1);
    printf("\n");

    printf("Enter the number of columns for matrix-A:");
    scanf("%d",&cols1);
    printf("\n");

    printf("Enter the number of rows for matrix-B:");
    scanf("%d",&rows2);
    printf("\n");

    printf("\n");
    
    printf("Enter the number of columns for matrix-B:");
    scanf("%d",&cols2);
    printf("\n");

    int A[rows1][cols1],B[rows2][cols2],choice;

    inputData(rows1,cols1,rows2,cols2,A,B);

    printf("\nMatrix Operations:1)Addition\n2)Subtraction\n3)Multiplication\n4)Transpose\n");
    scanf("%d",&choice);

    // Switch case:

    switch (choice){

        case 1:

            
            add(rows1,cols1,rows2,cols2,A,B);
            break;
        
        case 2:

            sub(rows1,cols1,rows2,cols2,A,B);
            break;

        case 3:

            multiply(rows1,cols1,rows2,cols2,A,B);
            break;

        case 4:

            trans(rows1,cols1,rows2,cols2,A,B);
            break;

        default:
            
            printf("Invalid Choice.");

    }




return 0;
}


// Function Definitions:

// 1) Data Input:

void inputData(int rows1,int cols1,int rows2,int cols2,int A[rows1][cols1],int B[rows2][cols2]){

    
    printf("Enter the elements for the matrix-A:");

    for(int i=0;i<rows1;i++){

        for(int j=0;j<cols1;j++){


        scanf("%d",&A[i][j]);

        }

    }


    printf("Enter the elements for the matrix-B:");

    for(int i=0;i<rows2;i++){

        for(int j=0;j<cols2;j++){


        scanf("%d",&B[i][j]);

        }

    }
}

// 2) Addition of Matrices:

void add(int rows1,int cols1,int rows2,int cols2,int A[rows1][cols1],int B[rows2][cols2]){

    if(rows1==rows2 && cols1==cols2){

        int C[rows1][cols1];


        int i,j;
        for(i=0;i<rows1;i++){

            for(j=0;j<cols1;j++){

            C[i][j]=A[i][j]+B[i][j];

    }
}
        printf("The addition of matrices is:\n");
        display(rows1,cols1,C);
    }

    else{
        printf("Order of the matrices are not equal.");
    }
}


// 3) Subtraction of matrices:

void  sub(int rows1,int cols1,int rows2,int cols2,int A[rows1][cols1],int B[rows2][cols2]){

    if(rows1==rows2 && cols1==cols2){

        int C[rows1][cols1];


        int i,j;
        for(i=0;i<rows1;i++){

            for(j=0;j<cols1;j++){

            C[i][j]=A[i][j]-B[i][j];

    }
}
        printf("The subtraction of matrices is:\n");
        display(rows1,cols1,C);
    }

    else{
        printf("Order of the matrices are not equal.");
    }
}

// 4) Multiplication of Matrices:

void  multiply(int rows1,int cols1,int rows2,int cols2,int A[rows1][cols1],int B[rows2][cols2]){

    if(cols1==rows2){

        
        int C[rows1][cols2];

        for(int i=0;i<rows1;i++){
            for(int j=0;j<cols2;j++){
                C[i][j]=0;
                for(int k=0;k<rows1;k++){
                    
                    C[i][j]+=A[i][k]*B[k][j];

    }
}
        }
        printf("The result of multiplication is:\n");
        display(rows1,cols2,C);

    }

    else{
        printf("Columns of the matrix-A and Rows of the matrix-B are not equal.");
}
}
// 5) Transpose of Matrix-A and Matrix-B:

void trans(int rows1,int cols1,int rows2,int cols2,int A[rows1][cols1],int B[rows2][cols2]){

// Matrix-A

    int C[cols1][rows1],i,j;

       for(i=0;i<rows1;i++){
        for(j=0;j<cols1;j++){

                    C[j][i]=A[i][j];

        }
    }
    printf("The transpose of matrix-A is:\n");
    display(cols1,rows1,C);

// Matrix-B:

    int D[cols2][rows2];

    for(i=0;i<rows2;i++){
        for(j=0;j<cols2;j++){

                D[j][i]=B[i][j];
        }
    }
    printf("The transpose of matrix-B is:\n");
    display(cols2,rows2,D);

    
    
}




    
// 6) Displaying resultant matrices:

void display(int rows,int cols,int C[rows][cols]){

    for(int i=0;i<rows;i++){

        for(int j=0;j<cols;j++){

            printf("%d ",C[i][j]);
            
        }
        printf("\n");
    }
    
}
