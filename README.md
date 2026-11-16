#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <math.h>

#define N 100
#define R 4

double CZYSTY_SIN(int i, double okres, double amplituda) {
    return amplituda * sin(2.0 * M_PI * i / okres);
}

double CZYSTY_PROST(int i, int okres, double amplituda, double wypelnienie) {
    int czas_w_okresie = i % okres;
    int prog_przelaczenia = (int)(okres * wypelnienie);

    if (czas_w_okresie < prog_przelaczenia) {
        return amplituda;
    } else {
        return -amplituda;
    }
}

void FILTR_USREDNIAJACY(double wejscie[], double wyjscie[], int n) {
    for (int i = 0; i < n; i++) {
        double suma = 0.0;
        for (int j = 0; j < R; j++) {
            if (i - j >= 0) {
                suma += wejscie[i - j];
            } else {
                suma += 0.0;
            }
        }
        wyjscie[i] = suma / R;
    }
}

int main() {
    srand(time(NULL));

    double okres_sin, amp_sin;
    int okres_prost;
    double amp_prost, wyp_prost;

    printf("--- PARAMETRY SYGNALU SINUSOIDALNEGO ---\n");
    printf("Podaj okres (np. 20.0): ");
    scanf("%lf", &okres_sin);
    printf("Podaj amplitude (np. 1.0): ");
    scanf("%lf", &amp_sin);

    printf("\n--- PARAMETRY SYGNALU PROSTOKATNEGO ---\n");
    printf("Podaj okres (liczba calkowita, np. 20): ");
    scanf("%d", &okres_prost);
    printf("Podaj amplitude (np. 1.0): ");
    scanf("%lf", &amp_prost);
    printf("Podaj wypelnienie (od 0.0 do 1.0, np. 0.5): ");
    scanf("%lf", &wyp_prost);
    printf("\nGenerowanie wynikow...\n\n");

    double czysty_sin[N], czysty_prost[N];
    double zaszumiony_sin[N], zaszumiony_prost[N];
    double filtr1_sin[N], filtr1_prost[N];

    for (int i = 0; i < N; i++) {
        czysty_sin[i] = CZYSTY_SIN(i, okres_sin, amp_sin);
        czysty_prost[i] = CZYSTY_PROST(i, okres_prost, amp_prost, wyp_prost);

        double szum = ( (rand() / (double)RAND_MAX) * 2.0 - 1.0 );

        zaszumiony_sin[i] = czysty_sin[i] + szum;
        zaszumiony_prost[i] = czysty_prost[i] + szum;
    }

    FILTR_USREDNIAJACY(zaszumiony_sin, filtr1_sin, N);
    FILTR_USREDNIAJACY(zaszumiony_prost, filtr1_prost, N);

    printf("Probka\tCz_Sin\tSzum_Sin\tFiltr_Sin\tCz_Prost\tSzum_Prost\tFiltr_Prost\n");

    for (int i = 0; i < N; i++) {
        printf("%d\t%.4f\t%.4f\t%.4f\t%.4f\t%.4f\t%.4f\n",
               i,
               czysty_sin[i],
               zaszumiony_sin[i],
               filtr1_sin[i],
               czysty_prost[i],
               zaszumiony_prost[i],
               filtr1_prost[i]);
    }
    return 0;
}

