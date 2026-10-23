#include<stdio.h>

void inputData(int rows,int cols,int O[rows][cols]);

void compact(int rows,int cols,int C[rows][3],int size,int O[rows][cols]);

void transpose(int size,int rows,int cols,int C[size][3]);

void fastTranspose(int size,int rows,int cols,int C[size][3]);

void display(int size,int T[size][3]);


int main(){

    int rows,cols,size;

    printf("Enter the number of rows:");
    scanf("%d",&rows);
    printf("\n");

    printf("Enter the number of columns:");
    scanf("%d",&cols);
    printf("\n");

    int O[rows][cols];
    inputData(rows,cols,O);
    int count=0;
    for(int i=0;i<rows;i++){
        for(int j=0;j<cols;j++){    
            if(O[i][j]!=0){
                count++;
            }
        }
    }

    size=count+1;
    
    int C[size][3];

    compact(rows,cols,C,size,O);

    int choice;
    printf("\nChoose Transpose Method:1)Simple Transpose\n2)Fast Transpose\n");
    scanf("%d",&choice);

    switch(choice){

        case 1:
            transpose(size,rows,cols,C);
            break;
        
        case 2:
            fastTranspose(size,rows,cols,C);
            break;

        default:
            printf("Invalid Choice.");

    }

return 0;
}

// 1) Input Elements for the Matrix:

void inputData(int rows,int cols,int O[rows][cols]){

    printf("Enter the elements of the matrices:");

    for(int i=0;i<rows;i++){
        for(int j=0;j<cols;j++){
            scanf("%d",&O[i][j]);
        }
    }
    printf("\n");
}


// 2) Compact form of the matrix:

void compact(int rows,int cols,int C[rows][3],int size,int O[rows][cols]){

    
    
    int k=1;

    C[0][0]=rows,C[0][1]=cols,C[0][2]=size-1;

    for(int i=0;i<rows;i++){
        for(int j=0;j<cols;j++){

            if(O[i][j]!=0){

                C[k][0]=i;
                C[k][1]=j;
                C[k][2]=O[i][j];
                k++;
            }
        }
    }
    printf("The compact matrix is:\n");
    display(size,C);


    
}


// 3) Transpose of Sparse Matrix:

void transpose(int size,int rows,int cols,int C[size][3]){


        int T[size][3],index;

        T[0][0]=cols,T[0][1]=rows;T[0][2]=size-1;



    int k = 1;

    for(int col = 0; col < cols; col++)
    {
        for(int i = 1; i < size; i++)
        {
            if(C[i][1] == col)
            {
            T[k][0] = C[i][1];
            T[k][1] = C[i][0];
            T[k][2] = C[i][2];
            k++;
            }
            }
    }
     
        printf("\nThe simple transpose matrix is:\n");
        display(size,T);
    }



// 4) Fast transpose:

void fastTranspose(int size,int rows,int cols,int C[size][3]){

    int total[cols],FT[size][3],index[cols],position;

    for(int i = 0; i < cols; i++)
{
    total[i] = 0;
}
    
    for(int i=1;i<size;i++){

        total[C[i][1]]++;
}

    index[0]=1;
    for(int i=1;i<cols;i++){

        index[i]=index[i-1]+total[i-1];
        
}


    FT[0][0]=cols;
    FT[0][1]=rows;
    FT[0][2]=size-1;

    for(int t=1;t<size;t++){

        
        position=index[C[t][1]];

        FT[position][0]=C[t][1];
        FT[position][1]=C[t][0];
        FT[position][2]=C[t][2];
        index[C[t][1]]++;

    }
printf("\nThe fast transpose matrix is:\n");
display(size,FT);
}
    
    
// 5) Display the  matrix:

void display(int size,int T[size][3]){

    

    for(int i=0;i<size;i++){

        for(int j=0;j<3;j++){

            printf("%d ",T[i][j]);
        }
        printf("\n");
    }
}

