#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <time.h>

#define N 100

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

void FILTR_USREDNIAJACY(const double wejscie[], double wejscie[], int n) {
    for (int i = 0; i < n; i++) {
            double suma = 0.0;

            for (int j = 0; j < r; j++ ) {
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

    double czysty_sin[N];
    double czysty_prost[N];
    double zaszumiony_sin[N];
    double zaszumiony_prost[N];
    double filtr_sin[N];
    double filtr_prost[N];

    SINUS(czysty_sin, N, 3.0, 20.0);

    PROSTOKAT(czysty_prost, N, 20, 10);

    DODAJ_SZUM(czysty_sin, zaszumiony_sin, N, 0.8);
    DODAJ_SZUM(czysty_prost, zaszumiony_prost, N, 0.8);

    FILTR_USREDNIAJACY(zaszumiony_sin, filtr_sin, N);
    FILTR_USREDNIAJACY(zaszumiony_prost, filtr_prost, N);

    printf("%-7s | %-12s | %-15s | %-14s | %-15s\n",
           "Probka", "Czysty Sin", "Sin z szumem", "Sin filtr" "Czysty Prost", "Prost z szumem", "Prost filtr");
    printf("--------|--------------|-----------------|----------------|-----------------|----------------|\n");

    for (int i = 0; i < 25; i++) {
        printf("%-7d | %-12.4f | %-15.4f | %-14.4f | %-15.4f\n",
               i, czysty_sin[i], zaszumiony_sin[i], filtr_sin[i],czysty_prost[i], zaszumiony_prost[i], filtr_prost[i]);
    }

    return 0;
}
