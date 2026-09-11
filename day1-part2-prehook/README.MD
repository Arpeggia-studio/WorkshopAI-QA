#Skrypty do analizy bezpieczeństwa (prehook)



w open code to jest plugin



```
Stwórz skrypt uruchamiany jako Plugin (pre-hook) przed rozpoczęciem sesji LLM.

Skrypt powinien analizować:

1. treść promptu przesyłanego przez użytkownika,
2. zawartość plików dołączonych do promptu.

Celem skryptu jest wykrycie danych osobowych:

• adresów e-mail,  
• numerów PESEL.

Logika działania:

1. Przed wysłaniem promptu do LLM uruchom skrypt walidujący.
2. Skrypt analizuje prompt oraz dołączone pliki.
3. Jeśli nie znajdzie adresu e-mail ani numeru PESEL:  
   • wyświetla informację, że nie wykryto danych,  
   • pozwala kontynuować wykonanie promptu przez LLM.
4. Jeśli znajdzie adres e-mail lub PESEL:  
   • zatrzymuje wysłanie promptu do LLM,  
   • zwraca listę znalezionych danych,  
   • wskazuje typ znalezionej wartości, np. EMAIL lub PESEL,  
   • wskazuje źródło, np. prompt albo nazwę pliku.

Przykładowy wynik:

EMAIL: jan.kowalski@example.com | źródło: prompt

PESEL: 85010112345 | źródło: prompt

Jeśli wykryto jakiekolwiek takie dane, prompt nie może zostać przekazany do LLM.

```
