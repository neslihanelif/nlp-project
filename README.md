# nlp-project
almanca veya ingilizce metinlerin dilini algılayan program
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdbool.h>
#include <math.h>


const char matrix_bigram_strings[10][3] ={"th",
                                            "he",
                                            "in",
                                            "er",
                                            "an", "en", "ch", "de", "ei", "te"};

const char matrix_trigram_strings[10][4]={"the", "and", "ing", "ent", "ion", "der", "sch", "ich", "nde", "die"};

const char languages[2][8]={"english", "german"};

//ingilizce dilinin frekans degerleri
const float frequency_eng[20]={ 2.71, 2.33, 2.03, 1.78, 1.61, 1.13, 0.01, 0.01, 0.01, 0.01, 1.81, 0.73, 0.72, 0.42, 0.42, 0.01, 0.01, 0.01, 0.01, 0.01 };
//Almanca dilinin frekans degerleri
const float frequency_germ[20]={ 0.01, 0.89, 1.71, 3.90, 1.07, 3.61, 2.36, 2.31, 1.98, 1.98, 0.01, 0.01, 0.01, 0.01, 0.01, 1.04, 0.76, 0.75, 0.72, 0.62 };


float calculated_frequencies[20];
float distances [2]={0,0};

void str_alici(char str[]);
void filter_str(char str[]); //Bu fonksiyon sayesinde kullanicidan alinan metinde yer alan yabanci karakterleri temizledik
void calculate_frequencies_bi(char str[]);//Metinde gecen bigram'larin frekansini hesaplamak icin kullandik
void calculate_frequencies_tri(char str[]);//Metinde gecen trigram'larin frekansini hesaplamak icin kullandik
void calculate_distances();
void detect_lang();

int main()
{
    char metin[8500];
    str_alici(metin);
    printf("\n\n");
    filter_str(metin);//Metindeki ingilizce harf(A-Z&&a-z),bosluk ve determinating karakter disinda herseyi bosluga cevirdik
    calculate_frequencies_bi(metin);
    calculate_frequencies_tri(metin);
    calculate_distances();
    detect_lang();

	return 0;
}

void str_alici(char str[])
{
    printf("Metninizi giriniz\n\n");

    gets(str);


}
void filter_str(char str[])
{


    for(int i=0;i<strlen(str);i++)
    {
       if((str[i]>='a'&&str[i]<='z')||(str[i]>='A'&&str[i]<='Z')||(str[i]==' ')||(str[i]=='\0')) //Determinating karakteri yok etmemeye ozen gosterdik
       {
            str[i]=tolower(str[i]);

       }

       else
       {
         str[i]=' ';
       }
    }

    puts(str);
    printf("\n");



}
void calculate_frequencies_bi(char str[])
{


    int i,j,sayac=0;


    for(int a=0;a<10;a++) //Bigramlar arasinda gecis yapmamizi saglar
    {
        for(i=0;i<strlen(str);i++) // "i" ile metni tarariz
        {
            for(j=0;j<2;j++) //Bigramin harflerini tarariz
                if(matrix_bigram_strings[a][j]!=str[i+j]) //Bigramlari sayar
                   break;

             if(j==2)
               sayac++;

        }
        //Her bir bigramin tekrar sayisi matrise atanir
        calculated_frequencies[a]=sayac;
        sayac=0;//Sayac yeni bir bigrami saymak icin sifirlanir

    }


    printf("\n{\n");
    for(int l=0;l<10;l++)
    {
        printf("\n %f,        %s\n",calculated_frequencies[l],matrix_bigram_strings[l]);

    }
     printf("\n}\n");

}
void calculate_frequencies_tri(char str[])
{

    int i,j,sayac=0;

    for(int a=0;a<10;a++)
    {
        for(i=0;i<strlen(str);i++)
        {
            for(j=0;j<3;j++)
                if(matrix_trigram_strings[a][j]!=str[i+j])
                   break;

             if(j==3)
                sayac++;

        }

        calculated_frequencies[a+10]=sayac;//9. indisde en son trigram bulunur
        sayac=0;

    }
    printf("\n{\n");
    for(int l=10;l<19;l++)
    {
        printf("\n %f,        %s\n",calculated_frequencies[l],matrix_trigram_strings[l-10]);

    }
     printf("\n}\n");

}
void calculate_distances()//Oklid hesaplamasinin saglikli sonuc vermedigini gordugumuz icin fizikteki kuvvet mantigini kullanarak yeni bir algoritma gelistirdik
{
    for(int i=0;i<20;i++)
    {
        distances[0]=distances[0]+calculated_frequencies[i]*frequency_eng[i];

        distances[1]=distances[1]+calculated_frequencies[i]*frequency_germ[i];
    }
}
void detect_lang()
{
    if(distances[0]/distances[1]>=1)//Metnin yazildigi dile karar verilen asama
        printf("\nMetinin dili ingilizcedir\n");

    else
        printf("\nMetinin dili almancadir\n");


}
