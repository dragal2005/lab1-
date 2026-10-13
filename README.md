#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <time.h>

// Definicja stałej zgodnie z poleceniem
#define N 100 // Długość wektora (N <= 100)[span_2](start_span)[span_2](end_span)

// ---------------------------------------------------------
// Zad. 1: Funkcja generująca sygnał sinusoidalny[span_3](start_span)[span_3](end_span)
// ---------------------------------------------------------
void SINUS(double wektor[], int n, double amplituda, double okres) {
    for (int i = 0; i < n; i++) {
        wektor[i] = amplituda * sin(2.0 * M_PI * i / okres);
    }
}

// ---------------------------------------------------------
// Zad. 2: Funkcja generująca sygnał prostokątny[span_4](start_span)[span_4](end_span)
// ---------------------------------------------------------
void PROSTOKAT(double wektor[], int n, int T, int t0) {
    if (t0 >= T) t0 = T - 1; // Zabezpieczenie przed błędem (t0 musi być < T)[span_5](start_span)[span_5](end_span)

    for (int i = 0; i < n; i++) {
        if ((i % T) < t0) {
            wektor[i] = 1.0;
        } else {
            wektor[i] = -1.0;
        }
    }
}

// ---------------------------------------------------------
// Zad. 3: Funkcja dodająca szum i zachowująca czysty sygnał[span_6](start_span)[span_6](end_span)
// ---------------------------------------------------------
// Funkcja odczytuje wejscie, dodaje zokrąglony szum i zapisuje wynik do wyjscia
void DODAJ_SZUM(const double wejscie[], double wyjscie[], int n, double amplituda_szumu) {
    for (int i = 0; i < n; i++) {
        // Wygenerowanie losowej wartości z przedziału [-1.0, 1.0]
        double znormalizowany_szum = ((double)rand() / RAND_MAX) * 2.0 - 1.0;
        
        // Zaokrąglenie do całości i przeskalowanie przez amplitudę
        double szum = round(znormalizowany_szum) * amplituda_szumu;
        
        // Zapis do nowej tablicy (czysty sygnał + szum)
        wyjscie[i] = wejscie[i] + szum;
    }
}

// ---------------------------------------------------------
// PROGRAM GŁÓWNY (MAIN)
// ---------------------------------------------------------
int main() {
    // Inicjalizacja generatora liczb losowych
    srand(time(NULL)); 

    // Deklaracja wektorów o zadanej długości N[span_7](start_span)[span_7](end_span)
    double czysty_sin[N];
    double czysty_prost[N];
    double zaszumiony_sin[N];
    double zaszumiony_prost[N];

    // --- WYWOŁANIA FUNKCJI ---

    // ZAD. 1: Generowanie sinusa (Amplituda: 3.0, Okres: 20 próbek)
    SINUS(czysty_sin, N, 3.0, 20.0);

    // ZAD. 2: Generowanie prostokąta (Okres: 20 próbek, Wypełnienie: 10 próbek)
    PROSTOKAT(czysty_prost, N, 20, 10);

    // ZAD. 3: Dodanie zaszumienia (Amplituda szumu: 0.8) do nowych wektorów[span_8](start_span)[span_8](end_span)
    DODAJ_SZUM(czysty_sin, zaszumiony_sin, N, 0.8);
    DODAJ_SZUM(czysty_prost, zaszumiony_prost, N, 0.8);

    // --- WYŚWIETLENIE WYNIKÓW ---
    // Wypisanie tabelaryczne do sprawdzenia wygenerowanych danych
    printf("%-7s | %-12s | %-15s | %-14s | %-15s\n", 
           "Probka", "Czysty Sin", "Sin z szumem", "Czysty Prost", "Prost z szumem");
    printf("--------|--------------|-----------------|----------------|-----------------\n");
    
    // Pętla ograniczona do 25 próbek dla czytelności (można zmienić na N)
    for (int i = 0; i < 25; i++) {
        printf("%-7d | %-12.4f | %-15.4f | %-14.4f | %-15.4f\n",
               i, czysty_sin[i], zaszumiony_sin[i], czysty_prost[i], zaszumiony_prost[i]);
    }

    return 0;
}
