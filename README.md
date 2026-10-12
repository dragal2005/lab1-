# lab1-
inf2 lab1 
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <time.h>

// Definicje stałych zgodnie z poleceniem
#define N 100 // Długość wektora (N <= 100)
#define R 4   // Rząd filtru (R < 5)

// ---------------------------------------------------------
// Zad. 1: Funkcja generująca sygnał sinusoidalny[cite: 1]
// ---------------------------------------------------------
void SINUS(double wektor[], int n, double amplituda, double okres) {
    for (int i = 0; i < n; i++) {
        wektor[i] = amplituda * sin(2.0 * M_PI * i / okres);
    }
}

// ---------------------------------------------------------
// Zad. 2: Funkcja generująca sygnał prostokątny[cite: 1]
// ---------------------------------------------------------
void PROSTOKAT(double wektor[], int n, int T, int t0) {
    if (t0 >= T) t0 = T - 1; // Zabezpieczenie przed błędem (t0 musi być < T)[cite: 1]

    for (int i = 0; i < n; i++) {
        if ((i % T) < t0) {
            wektor[i] = 1.0;
        } else {
            wektor[i] = -1.0;
        }
    }
}

// ---------------------------------------------------------
// Zad. 3: Funkcja dodająca szum z użyciem funkcji round[cite: 1]
// ---------------------------------------------------------
void DODAJ_SZUM(double wektor[], int n, double amplituda_szumu) {
    for (int i = 0; i < n; i++) {
        // Wygenerowanie losowej wartości z przedziału [-1.0, 1.0]
        double znormalizowany_szum = ((double)rand() / RAND_MAX) * 2.0 - 1.0;
        
        // Zaokrąglenie (da to -1.0, 0.0 lub 1.0) i przeskalowanie przez amplitudę
        double szum = round(znormalizowany_szum) * amplituda_szumu;
        
        // Dodanie szumu do pierwotnego wykresu
        wektor[i] += szum;
    }
}

// ---------------------------------------------------------
// Zad. 4: Funkcja realizująca filtr uśredniający rzędu R[cite: 1]
// ---------------------------------------------------------
void FILTR_USREDNIAJACY(const double wejscie[], double wyjscie[], int n) {
    for (int i = 0; i < n; i++) {
        double suma = 0.0;
        int licznik = 0;
        
        for (int j = 0; j < R; j++) {
            if (i - j >= 0) { // Zabezpieczenie przed wyjściem poza początek tablicy
                suma += wejscie[i - j];
                licznik++;
            }
        }
        wyjscie[i] = suma / licznik;
    }
}

// ---------------------------------------------------------
// PROGRAM GŁÓWNY (MAIN)
// ---------------------------------------------------------
int main() {
    // Inicjalizacja generatora liczb losowych dla szumu
    srand(time(NULL)); 

    // Deklaracja wektorów o zadanej długości N[cite: 1]
    double sygnal_sin[N];
    double sygnal_prost[N];
    double filtr_sin[N];
    double filtr_prost[N];

    // --- WYWOŁANIA FUNKCJI ---

    // ZAD. 1: Generowanie sinusa (Amplituda: 3.0, Okres: 20 próbek)
    SINUS(sygnal_sin, N, 3.0, 20.0);

    // ZAD. 2: Generowanie prostokąta (Okres: 20 próbek, Wypełnienie: 10 próbek)
    PROSTOKAT(sygnal_prost, N, 20, 10);

    // ZAD. 3: Dodanie zaszumienia (Amplituda szumu: 0.8) do obu wektorów[cite: 1]
    DODAJ_SZUM(sygnal_sin, N, 0.8);
    DODAJ_SZUM(sygnal_prost, N, 0.8);

    // ZAD. 4: Zastosowanie filtru uśredniającego i zapis wyników do nowych wektorów[cite: 1]
    FILTR_USREDNIAJACY(sygnal_sin, filtr_sin, N);
    FILTR_USREDNIAJACY(sygnal_prost, filtr_prost, N);

    // --- WYŚWIETLENIE WYNIKÓW ---
    // Wypisanie tabelaryczne do sprawdzenia wygenerowanych danych
    printf("%-7s | %-15s | %-15s | %-15s | %-15s\n", 
           "Probka", "Sin z szumem", "Sin po filtrze", "Prost z szumem", "Prost po filtrze");
    printf("--------|-----------------|-----------------|-----------------|-----------------\n");
    
    // Pętla do N, czyli wyświetli całe 100 próbek[cite: 1]
    for (int i = 0; i < N; i++) {
        printf("%-7d | %-15.4f | %-15.4f | %-15.4f | %-15.4f\n",
               i, sygnal_sin[i], filtr_sin[i], sygnal_prost[i], filtr_prost[i]);
    }

    return 0;
}
