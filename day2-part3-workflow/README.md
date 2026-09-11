prompt



Stwórz mi workflow agentów, które będą wykonywać określone cele:

1. Playwright Developer bazując na wejściu, dostanie scenariusz testowy, zrobi analizę na podstawie dostępnej aplikacji i linku oraz przygotuje scenariusz testowy.  Bedzie uzywal skill do programowania w playwright.

2. Service Locator Specialist - agent będzie miał za cel dobierać i zarządzać interakcją z UI. Jeżeli coś nie działa, to on to naprawia i przygotowuje odpowiednie locatory/selectory.

3. Test Reviewer - agent, który sprawdza scenariusz pytaniami, ale nie poprawia kodu:
   czy jest dobre assertion,
   czy test naprawdę sprawdza wymaganie,
   czy assertion jest wystarczający,
   czy test może przejść mimo błędu aplikacji,
   czy są race conditions,
   czy użyto waitForTimeout,
   czy test zależy od kolejności innych testów,
   czy locator jest stabilny.

4. Agent uruchamia test, analizuje trace, screenshoty, console logi, network i error stack. Jego outputem powinna być diagnoza.

5. Test Architecture Agent - to agent „senior”. Nie zajmuje się pojedynczym scenariuszem, tylko pilnuje całego repozytorium. Wykrywa duplikację Page Objectów, niepotrzebne helpery, złe fixtures, zależności między testami, zbyt długie testy, problemy z parallel execution i powtarzające się setupy.
