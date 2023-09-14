# Euroleague-Simulation
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>

#define BOYUT 18

//random sayı üret grup aşaması
int func1(int i)
{
	if (i < (BOYUT / 2))
	{
		return rand() % 30 + 70;
	}

	if (i > (BOYUT / 2) || i < BOYUT)
	{
		return rand() % 40 + 60;
	}
}

//
int func2()
{
	return rand() % 50 + 70;
}

//Puan durumundaki istatiskleri tutmak için dizigenel matrisi olusturlmuş ve takımlar puanlarına göre sılanmış.Puanları eşit takımlar averajlarına göre averajları eşit takımların gol atma sayılarına göre sıralaması olmustur.
void func3(int dizigenel[BOYUT][9]) {
	int i, j, t;

	for (i = 0; i < BOYUT; i++) {
		for (j = i + 1; j < BOYUT; j++) {
			if (dizigenel[j][8] >= dizigenel[i][8]) {
				for (int k = 0; k < 9; k++) {
					t = dizigenel[i][k];
					dizigenel[i][k] = dizigenel[j][k];
					dizigenel[j][k] = t;
				}
			}

			if (dizigenel[i][8] == dizigenel[j][8]) {
				if (dizigenel[j][7] >= dizigenel[i][7]) {
					for (int k = 0; k < 9; k++) {
						t = dizigenel[i][k];
						dizigenel[i][k] = dizigenel[j][k];
						dizigenel[j][k] = t;
					}
				}
			}

			if (dizigenel[i][8] == dizigenel[j][8]) {
				if (dizigenel[j][7] == dizigenel[i][7]) {
					if (dizigenel[j][5] >= dizigenel[i][5]) {
						for (int k = 0; k < 9; k++) {
							t = dizigenel[i][k];
							dizigenel[i][k] = dizigenel[j][k];
							dizigenel[j][k] = t;
						}
					}
				}
			}
		}
	}
}

int main() {
	int dizi1[BOYUT];
	const char* dizi[BOYUT] = { "Olympiakos","Barcelona","Real Madrid","Monaco","Maccabi Tel Aviv","Partizan","Zalgris","Fenerbahce","Cazo Baskonia","Kizilyildiz","Anadolu Efes","Olimpia Milano"
						,"Valencia","Bologna","Bayern Munih","Alba Berlin","Panathinaikos","Asvel" };


	int bosluk[BOYUT];//takım isimlerinin karakter uzunluğu
	for (int i = 0; i < BOYUT; i++)
	{
		bosluk[i] = strlen(dizi[i]);
	}

	int g[BOYUT] = { 0 };//galibiyet sayısı
	int y[BOYUT] = { 0 };//yenilgi sayısı
	int at[BOYUT] = { 0 };//atılan gol
	int yen[BOYUT] = { 0 };//yenilen gol
	int say;//oynanan maç sayısı

	srand(time(NULL));
	for (int i = 0; i < BOYUT; i++)//i iç saha takımlarınıj dış saha takımlarını temsil ederek birbirleriyle eşleşme sağlanır.i=j olduğu zaman yapılmaması için if deyimi yazılır.
	{
		say = 0;

		for (int j = 0; j < BOYUT; j++)
		{

			dizi1[i] = func1(i);
			dizi1[j] = func1(j);

			if (i != j)
			{
				do
				{
					dizi1[i] = func1(i);
					dizi1[j] = func1(j);
				} while (dizi1[i] == dizi1[j]);

				if (dizi1[i] > dizi1[j])
				{
					g[i]++;
					say++;
				}

				else if (dizi1[i] < dizi1[j])
				{
					y[i]++;
					say++;
				}

				//Dış Saha Maçları
				if (dizi1[j] > dizi1[i])
				{
					g[j]++;
					say++;
				}

				else if (dizi1[j] < dizi1[i])
				{
					y[j]++;
					say++;
				}

				at[i] += dizi1[i];//iç sahada atılan gol
				yen[i] += dizi1[j];//iç sahada yenilen gol
				at[j] += dizi1[j];//dış sahada atılan gol
				yen[j] += dizi1[i];//dış sahada yenilen gol
			}
		}
	}


	int av[BOYUT];
	int puan[BOYUT];

	for (int i = 0; i < BOYUT; i++)
	{
		puan[i] = 2 * g[i];//puan
		av[i] = at[i] - yen[i];//averaj
	}

	int dizigenel[BOYUT][9];//bütün verilerin tutulduğu tablo

	for (int i = 0; i < BOYUT; i++)
	{
		dizigenel[i][0] = (int)dizi[i];//string karakterli takımların diziye dahil edebilmek için tür dönüştürücü
		dizigenel[i][1] = bosluk[i];//takım isimlerinin karakter sayısı
		dizigenel[i][2] = say;//maç sayısı
		dizigenel[i][3] = g[i];//galibiyet sayısı
		dizigenel[i][4] = y[i];//yenilgi sayısı
		dizigenel[i][5] = at[i];//atılan gol sayısı
		dizigenel[i][6] = yen[i];//yenilen gol sayısı
		dizigenel[i][7] = av[i];//averaj sayısı
		dizigenel[i][8] = puan[i];//toplanan puan

	}

	func3(dizigenel);//fonksiyon 3e göre veriler sıralanır

	int gU[BOYUT]; //galibiyet sayısı karakter 
	int yU[BOYUT];//yenilgi sayısı karakter
	int atU[BOYUT];//atılan gol karakter sayısı
	int yenU[BOYUT];//yenilen gol karakter sayısı
	int avU[BOYUT];//averaj karakter sayısı
	int sayi[BOYUT];//oynanan maç karakter sayısı

	for (int i = 0; i < BOYUT; i++)//yazdırma işlemi için gerekli bosluklar doğru yazılsın diye boşluk ayarı için hesaplamalar yapılır
	{
		sayi[i] = snprintf(NULL, 0, "%d", i + 1);
		gU[i] = snprintf(NULL, 0, "%d", dizigenel[i][3]);
		yU[i] = snprintf(NULL, 0, "%d", dizigenel[i][4]);
		atU[i] = snprintf(NULL, 0, "%d", dizigenel[i][5]);
		yenU[i] = snprintf(NULL, 0, "%d", dizigenel[i][6]);
		avU[i] = snprintf(NULL, 0, "%d", abs(dizigenel[i][7]));

	}

	printf("\n");
	printf("\033[36mPUAN DURUMU              O     G    M     A      Y      Ave  P \033[0m\n");

	for (int i = 0; i < BOYUT; i++)
	{
		if (i < (BOYUT - 10))
		{
			printf("\033[33m%d)%*s %s %*s %d %*s %d %*s %d %*s %d %*s %d %*s %*s%*s%d %*s %d\033[0m\n", i + 1, 2 - sayi[i], "", dizigenel[i][0], 19 - dizigenel[i][1], "", dizigenel[i][2], 2, "", dizigenel[i][3], 3 - gU[i], "", dizigenel[i][4], 3 - yU[i], "", dizigenel[i][5], 3 - atU[i], "", dizigenel[i][6], 3 - yenU[i], "", 0, dizigenel[i][7] == 0 ? " " : "", 0, dizigenel[i][7] > 0 ? "+" : "", dizigenel[i][7], 3 - avU[i], "", dizigenel[i][8]);
		}

		else
		{
			printf("%d)%*s %s %*s %d %*s %d %*s %d %*s %d %*s %d %*s %*s%*s%d %*s %d\n", i + 1, 2 - sayi[i], "", dizigenel[i][0], 19 - dizigenel[i][1], "", dizigenel[i][2], 2, "", dizigenel[i][3], 3 - gU[i], "", dizigenel[i][4], 3 - yU[i], "", dizigenel[i][5], 3 - atU[i], "", dizigenel[i][6], 3 - yenU[i], "", 0, dizigenel[i][7] == 0 ? " " : "", 0, dizigenel[i][7] > 0 ? "+" : "", dizigenel[i][7], 3 - avU[i], "", dizigenel[i][8]);
		}
    }

	printf("\n");

	int Ceyrekfinal[8];
	int Yarifinaltakim[4];

	//Ceyrek final maçları için 8 skor üretir.
	for (int i = 0; i < 8; i++)
	{
		Ceyrekfinal[i] = func2();
	}

	//(0-7) (1-6) (2-5) (3-4) ile oynar.

	do
	{
		Ceyrekfinal[7] = func2();
	} while (Ceyrekfinal[0] == Ceyrekfinal[7]);

	if (Ceyrekfinal[0] > Ceyrekfinal[7])
		Yarifinaltakim[0] = dizigenel[0][0];
	if (Ceyrekfinal[0] < Ceyrekfinal[7])
		Yarifinaltakim[0] = dizigenel[7][0];

	do
	{
		Ceyrekfinal[6] = func2();
	} while (Ceyrekfinal[1] == Ceyrekfinal[6]);

	if (Ceyrekfinal[1] > Ceyrekfinal[6])
		Yarifinaltakim[1] = dizigenel[1][0];
	if (Ceyrekfinal[1] < Ceyrekfinal[6])
		Yarifinaltakim[1] = dizigenel[6][0];

	do
	{
		Ceyrekfinal[5] = func2();
	} while (Ceyrekfinal[2] == Ceyrekfinal[5]);

	if (Ceyrekfinal[2] > Ceyrekfinal[5])
		Yarifinaltakim[2] = dizigenel[2][0];
	if (Ceyrekfinal[2] < Ceyrekfinal[5])
		Yarifinaltakim[2] = dizigenel[5][0];

	do
	{
		Ceyrekfinal[4] = func2();
	} while (Ceyrekfinal[3] == Ceyrekfinal[4]);

	if (Ceyrekfinal[3] > Ceyrekfinal[4])
		Yarifinaltakim[3] = dizigenel[3][0];
	if (Ceyrekfinal[3] < Ceyrekfinal[4])
		Yarifinaltakim[3] = dizigenel[4][0];

	//Ceyrek final skorlarının karakter uzunluğu
	int CeyrekfinalU[8];

	for (int i = 0; i < 8; i++)
	{
		CeyrekfinalU[i] = snprintf(NULL, 0, "%d", Ceyrekfinal[i]);
	}

	printf("\033[33mCEYREK FINAL\033[0m\n");
	for (int i = 0; i < 4; i++)
	{
		printf("%s %*s %*s %d %*s %d %*s %s\n", dizigenel[i][0], 17 - dizigenel[i][1], "", 4 - CeyrekfinalU[i], "", Ceyrekfinal[i], 3 - CeyrekfinalU[7 - i], "", Ceyrekfinal[7 - i], 9, "", dizigenel[7 - i][0]);
	}

	printf("\n");

	int Yarifinal[4];
	int Finaltakim[2];

	//4 tane Yarı final skoru üretilir
	for (int i = 0; i < 4; i++)
	{
		Yarifinal[i] = func2();
	}

	// (0-7) galibi (2-5) galibi ile oynar.(1-6) galibi (3-4) galibi ile oynar.
	do
	{
		Yarifinal[3] = func2();
	} while (Yarifinal[0] == Yarifinal[3]);

	if (Yarifinal[0] > Yarifinal[3])
		Finaltakim[0] = Yarifinaltakim[0];
	if (Yarifinal[0] < Yarifinal[3])
		Finaltakim[0] = Yarifinaltakim[2];

	do
	{
		Yarifinal[2] = func2();
	} while (Yarifinal[1] == Yarifinal[2]);

	if (Yarifinal[1] > Yarifinal[2])
		Finaltakim[1] = Yarifinaltakim[1];
	if (Yarifinal[1] < Yarifinal[2])
		Finaltakim[1] = Yarifinaltakim[3];

	//Yarifinal skorları karakter uzunlukları
	int YarifinalU[4];
   for (int i = 0; i < 4; i++)
	{
		YarifinalU[i] = snprintf(NULL, 0, "%d", Yarifinal[i]);
	}

   printf("\033[33mYARI FINAL\033[0m\n");
  for (int i = 0; i < 2; i++)
	{
		printf("%s %*s %*s %d   %d %*s %*s %s\n", Yarifinaltakim[i], Ceyrekfinal[i] > Ceyrekfinal[7 - i] ? 18 - dizigenel[i][1] : 18 - dizigenel[7 - i][1], "", 3-YarifinalU[i], "", Yarifinal[i], Yarifinal[i + 2], 3 - YarifinalU[i+2], "",7,"", Yarifinaltakim[i + 2]);
	}
	printf("\n");

	
	int Final[2];
	//2 farklı skor üretilir
	for (int i = 0; i < 2; i++)
	{
		Final[i] = func2();
	}
	
	//skorların eşit olmaması sağlanır
	do
	{
		Final[1] = func2();
	} while (Final[0] == Final[1]);

	int FinalU[2];
	for (int i = 0; i < 2; i++)
	{
		FinalU[i] = snprintf(NULL, 0, "%d", Final[i]);
	}

	printf("\033[33mFINAL\033[0m\n");
    printf("%s %*s %*s %d   %d %*s %s\n\n", Finaltakim[0], Yarifinal[0] > Yarifinal[3] ? (Ceyrekfinal[0] > Ceyrekfinal[7] ? 18 - dizigenel[0][1] : 18 - dizigenel[7][1]) : (Ceyrekfinal[2] > Ceyrekfinal[5] ? 18 - dizigenel[2][1] : 18 - dizigenel[5][1]),"",
		3-FinalU[0],"", Final[0], Final[1], 11 - FinalU[1], "", Finaltakim[1]);



	if (Final[0] > Final[1])
	{
		printf("\033[31mSampiyon:%s\033[0m\n\n", Finaltakim[0]);
	}
	
	if (Final[0] < Final[1])
	{
		printf("\033[31mSampiyon:%s\033[0m\n\n", Finaltakim[1]);
	}

	return 0;
	

}

