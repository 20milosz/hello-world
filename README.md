#include <stdio.h>
#include <stdlib.h>
#include <complex.h>
#include <math.h>
#include <time.h>

#define Complex double complex

Complex * fft(const Complex *input, int size)
{
    if(size==2)
    {
        Complex *output = calloc(2, sizeof(Complex));
        output[0] = input[1] + input[0];
        output[1] = input[1] - input[0];
        return output;
    }
    else
    {
        int i;
        Complex *output= calloc(size,   sizeof(Complex));
        Complex *even  = calloc(size/2, sizeof(Complex));
        Complex *odd   = calloc(size/2, sizeof(Complex));
        Complex *even_ret;
        Complex *odd_ret;

        for(i=0; i<size/2; i++)
        {
            even[i]=input[2*i];
            odd[i] =input[2*i+1];
        }
        even_ret = fft(even, size/2);
        odd_ret  = fft(odd, size/2);

        free(even);
        free(odd);

        for(i=0; i<size/2; i++)
        {
            output[i]=even_ret[i]+odd_ret[i]*(cos(-2*M_PI/size*i)+I*sin(-2*M_PI/size*i));
        }
        for(i=0; i<size/2; i++)
        {
            output[i + size/2] = even_ret[i] + odd_ret[i]*(
                                     cos(-2*M_PI/size*(i+size/2)) + I*sin(-2*M_PI/size*(i+size/2))
                                 );
        }
        free(even_ret);
        free(odd_ret);

        return output;

    }
}

int main()
{
    int j;
    int size;
    int i;
    for(j=1; j<19; j++)
    {
        size=pow((double)2, (double)j);
        Complex input[size];

        for(i=0; i<size; i++)
        {
            input[i]=sin(2*M_PI*i/size);
        }

        double c_start = 1000*clock()/((double)CLOCKS_PER_SEC);

        Complex *output=fft(input, size);

        double c_stop = 1000*clock()/((double)CLOCKS_PER_SEC);

        printf("rozmiar %d \n czas fft w milisenkundach: %f \n", size, c_stop-c_start);
        free(output);

    }
    for(i=0; i<size; i++)
    {
        //   printf("%f ", sqrt(creal(output[i])*creal(output[i])+cimag(output[i])*cimag(output[i])));
    }

    return 0;
}
