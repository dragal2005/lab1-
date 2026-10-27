#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <math.h>

#define N 100
#define R 4

// Funkcja generująca sygnał sinusoidalny
double CZYSTY_SIN(int i, double okres, double amplituda) {
    return amplituda * sin(2.0 * M_PI * i / okres);
}

// Funkcja generująca sygnał prostokątny
double CZYSTY_PROST(int i, int okres, double amplituda, double wypelnienie) {
    int czas_w_okresie = i % okres;
    int prog_przelaczenia = (int)(okres * wypelnienie);

    if (czas_w_okresie < prog_przelaczenia) {
        return amplituda;
    } else {
        return -amplituda;
    }
}

// Funkcja realizująca filtr kroczący uśredniający
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

    // --- ZMIENNE NA DANE Z KLAWIATURY ---
    double okres_sin, amp_sin;
    int okres_prost;
    double amp_prost, wyp_prost;

    // --- POBIERANIE DANYCH OD UŻYTKOWNIKA ---
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

    // --- INICJALIZACJA TABLIC ---
    double czysty_sin[N], czysty_prost[N];
    double zaszumiony_sin[N], zaszumiony_prost[N];
    double filtr1_sin[N], filtr1_prost[N];
    double filtr2_sin[N], filtr2_prost[N];

    // --- GENEROWANIE SYGNALOW ---
    for (int i = 0; i < N; i++) {
        // Przekazanie zmiennych wpisanych z klawiatury do funkcji
        czysty_sin[i] = CZYSTY_SIN(i, okres_sin, amp_sin);
        czysty_prost[i] = CZYSTY_PROST(i, okres_prost, amp_prost, wyp_prost);

        double szum = ( (rand() / (double)RAND_MAX) * 2.0 - 1.0 );
        
        zaszumiony_sin[i] = czysty_sin[i] + szum;
        zaszumiony_prost[i] = czysty_prost[i] + szum;
    }

    // --- FILTRACJA ---
    FILTR_USREDNIAJACY(zaszumiony_sin, filtr1_sin, N);
    FILTR_USREDNIAJACY(zaszumiony_prost, filtr1_prost, N);

    FILTR_USREDNIAJACY(filtr1_sin, filtr2_sin, N);
    FILTR_USREDNIAJACY(filtr1_prost, filtr2_prost, N);

    // --- WYPISYWANIE WYNIKOW ---
    printf("Probka\tCz_Sin\tSzum_Sin\tFiltr_Sin\tCz_Prost\tSzum_Prost\tFiltr_Prost\n");

    for (int i = 0; i < N; i++) {
        printf("%d\t%.4f\t%.4f\t%.4f\t%.4f\t%.4f\t%.4f\n",
               i,
               czysty_sin[i],
               zaszumiony_sin[i],
               filtr2_sin[i],
               czysty_prost[i],
               zaszumiony_prost[i],
               filtr2_prost[i]);
    }

    // Zatrzymanie okna konsoli, aby wyniki nie zniknely od razu (opcjonalnie)
    system("pause");
    return 0;
}
