#define MIN     0
#define MAX     1
#define ODD     2
#define EVEN    3
#define MINMAX  4
#define ODDEVEN 5
#include <stdlib.h>
#include <stdio.h>
#include <stdlib.h>
#include "io.h"
#include "buffer.h"
#include "memory.h"

int *inputBuffer = NULL;
int *outputBuffer = NULL;

//int inputBuffer[Input_Local_BUFFER__SIZE] = {0};



// two functions provided to you (defined in io.h and io.c) are:
// int get_value(void);4
//       this function generates a single value
//       you use it to obtain this value and put it into an input buffer
// int submit_result(int[]);
//       you use this function to submit the data processing results
//       for validation (your results are checked inside this function)

int reading() {

    int operation_ID = get_value();//First assign the first value as the operation_ID, and then check whether it is -1
    if (operation_ID == -1){
        return -1;
    }
    int no_of_data_points = get_value();//get value as the number of the data points
    inputBuffer = (int*)my_malloc((no_of_data_points+2)* sizeof(int));//inputBuffer is set as an pointer, in some ways, we can understand it as a list
    //an empty list with given spaces (seen as no_of_data_points+2)*sizeof(int)
    inputBuffer[0] = operation_ID;//we insert values to the list one by one
    inputBuffer[1] = no_of_data_points;

    for (int i=0;i<inputBuffer[1];i++)
        inputBuffer[i+2] = get_value();
    return no_of_data_points;
}

void transferringTOlocal(int* localBuffer) {//here we set a pointer, same as above, it is still an empty list
    for (int i = 0; i < inputBuffer[1]+2;i++){
        localBuffer[i] = inputBuffer[i];//we put the data of inputBuffer all into the localBuffer

    }
    my_free(inputBuffer);//free the using of inputBuffer pointer
}


void processing(int localBuffer[]) {

    if (localBuffer[0] == MIN){
        int i = 10000000;// in this step we compare all the values with the "min value", 10000, if there is a value smaller than it, we substitute the "min value"
        for (int j = 0;j<localBuffer[1];j++) {// we found. After checking all the numbers, that is, after comparing all the numbers, we can find the real minimum value
            if (localBuffer[j + 2] < i) {// in the array. Finding the minimum value, we assign it to the third place to the localBuffer.
                localBuffer[2] = localBuffer[j + 2];
                i = localBuffer[j+2];
            }
        }
        localBuffer[1] = 1;//the second position shows how many results we are going to show
        localBuffer[3] = 0;//since we only show 1 result, the fourth position is 0
    }
    if (localBuffer[0] == MAX){//this is quite similar to what we did when localBuffer[0] = MIN, but what we did is just compare and find the bigger value.
        int k = 0;
        for (int a = 0;a< localBuffer[1]; a++) {
            if (localBuffer[a + 2] >= k) {
                localBuffer[2] = localBuffer[a + 2];
                k = localBuffer[a+2];
            }
        }
        localBuffer[1] = 1;
        localBuffer[3] = 0;
    }
    if (localBuffer[0] == ODD){//here we are going to find the numbers of the odd numbers. The character of odd numbers is that
        int odd_number2 = 0;// the remainder is not 0 after divided by 2. So every time we found a number whose remainder is not 0
        for (int b = 0; b < localBuffer[1]; b++){// after be divided by 2, we add the numbers of the odd number(odd_number2) with 1
            if (localBuffer[b+2]%2 != 0){// which makes it possible to count the amount of the odd numbers.
                odd_number2 ++;
            }
        }
        localBuffer[1] = 1;
        localBuffer[2] = odd_number2;
        localBuffer[3] = 0;
    }
    if (localBuffer[0] == EVEN){// this is step is quite similar to what we have done before. However, the character of even numbers is
        int even_number3 = 0;// that the remainder is 0 after be divided by 2.
        for (int c = 0; c < localBuffer[1]; c++){
            if (localBuffer[c+2]%2 == 0){
                even_number3 ++;
            }
        }
        localBuffer[1] = 1;
        localBuffer[2] = even_number3;
        localBuffer[3] = 0;
    }

    if (localBuffer[0] == MINMAX){
        int min = 10000;//we just combine the situation when localBuffer[0] = MIN and MAX. The only change is the variable names
        for (int j = 0;j<localBuffer[2];j++) {// and the reservation of the minimum and maximum value. If we assign the values directly
            if (localBuffer[j+2] < min) {// to the array, the numbers in the array will change which makes it impossible to show the
                min = localBuffer[j+2];// original values
            }
        }
        int max = 0;
        for (int a = 0;a< localBuffer[1]; a++) {
            if (localBuffer[a+2] >= max) {
                max = localBuffer[a+2];
            }
        }
        localBuffer[1] = 2;//there are 2 results shown, so the number on the second position is 2.
        localBuffer[2] = min;
        localBuffer[3] = max;

    }

    if (localBuffer[0] == ODDEVEN){// we just combine the situation when localBuffer[0] = ODD and EVEN. The only change is the variable names.
        int odd_number5 = 0;
        for (int d = 0; d < localBuffer[1]; d++){
            if (localBuffer[d+2]%2 != 0){
                odd_number5 ++;
            }
        }

        int even_number5 = 0;
        for (int e = 0; e < localBuffer[1]; e++){
            if (localBuffer[e+2]%2 == 0){
                even_number5 ++;
            }
        }

        localBuffer[1] = 2;
        localBuffer[2] = odd_number5;
        localBuffer[3] = even_number5;
    }

};
// this function transfers data between two buffers
// it is used to transfer data from input buffer to local buffer
// and to transfer data from local buffer to output buffer

void transferringFROMlocal(int* localBuffer) {// we construct a pointer, in some ways, an empty list
    outputBuffer = (int*)my_malloc((localBuffer[1]+2)*sizeof(int));
    for (int i = 0; i < localBuffer[1]+2;i++){
        outputBuffer[i] = localBuffer[i];//we put the data of inputBuffer all into the localBuffer

    }
}

void submitting() {
    int output = submit_results(outputBuffer);//take use of submit_results function
    my_free(outputBuffer);//free the using of outputBuffer pointer
    if (output == 0)
    {
        printf("The results are correct, the return value is 0\n");//if the result is correct, return 0
    }
    else{
        printf("The results are not correct, the return value is 1\n");//if not, return 1
    }


