#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <time.h>

#define N 100
#define R 4

void SINUS(double wektor[], int n, double amplituda, double okres) {
    for (int i = 0; i < n; i++) {
        wektor[i] = amplituda * sin(2.0 * M_PI * i / okres);
    }
}

void PROSTOKAT(double wektor[], int n, int T, int t0) {
    if (t0 >= T) t0 = T - 1;
    for (int i = 0; i < n; i++) {
        if ((i % T) < t0) {
            wektor[i] = 1.0;
        } else {
            wektor[i] = -1.0;
        }
    }
}

void DODAJ_SZUM(const double wejscie[], double wyjscie[], int n, double amplituda_szumu) {
    for (int i = 0; i < n; i++) {
        double znormalizowany_szum = ((double)rand() / RAND_MAX) * 2.0 - 1.0;
        double szum = round(znormalizowany_szum) * amplituda_szumu;
        wyjscie[i] = wejscie[i] + szum;
    }
}

void FILTR_USREDNIAJACY(const double wejscie[], double wyjscie[], int n) {
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

    double czysty_sin[N], czysty_prost[N];
    double zaszumiony_sin[N], zaszumiony_prost[N];
    double filtr1_sin[N], filtr2_sin[N];
    double filtr1_prost[N], filtr2_prost[N];

    SINUS(czysty_sin, N, 3.0, 20.0);
    PROSTOKAT(czysty_prost, N, 20, 10);

    DODAJ_SZUM(czysty_sin, zaszumiony_sin, N, 0.8);
    DODAJ_SZUM(czysty_prost, zaszumiony_prost, N, 0.8);

    FILTR_USREDNIAJACY(zaszumiony_sin, filtr1_sin, N);
    FILTR_USREDNIAJACY(filtr1_sin, filtr2_sin, N);

    FILTR_USREDNIAJACY(zaszumiony_prost, filtr1_prost, N);
    FILTR_USREDNIAJACY(filtr1_prost, filtr2_prost, N);

    printf("Probka\tCz_Sin\tSzum_Sin\tFiltr_Sin\tCz_Prost\tSzum_Prost\tFiltr_Prost\n");

    for (int i = 0; i < N; i++) {
        printf("%d\t%.4f\t%.4f\t\t%.4f\t\t%.4f\t\t%.4f\t\t%.4f\n",
               i, czysty_sin[i], zaszumiony_sin[i], filtr2_sin[i],
               czysty_prost[i], zaszumiony_prost[i], filtr2_prost[i]);
    }

    return 0;
}
