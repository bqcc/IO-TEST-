# Klasy – Łukasz Bera (obszar: Mechanik)

Obszar obejmuje zlecenia naprawy, wykonane naprawy, historię statusów i
kosztorysy dodatkowych prac. Powiązane przypadki użycia: *Wprowadź wykonaną
naprawę*, *Zgłoś dodatkową usterkę*, *Zmień status zlecenia naprawy*.

Klasy: `RepairOrderService`, `RepairOrderServiceImpl`, `Mechanic`,
`RepairOrder`, `RepairOrderStatus`, `StatusChange`, `Repair`, `CostEstimate`,
`EstimateStatus`.

## RepairOrderService (interfejs)

Rejestr zleceń naprawy i mechaników. Sama logika zlecenia jest w klasie
`RepairOrder`.

| Metoda | Co robi |
|---|---|
| `addOrder(order): void` | Zapisuje nowe zlecenie. |
| `findOrder(id): RepairOrder` | Zwraca zlecenie o podanym identyfikatorze. |
| `findOrdersByMechanic(mechanic): List<RepairOrder>` | Lista „Moje zlecenia” mechanika. |
| `findOrdersByClient(client): List<RepairOrder>` | Zlecenia klienta – do śledzenia statusu. |
| `findServiceHistory(vehicle): List<RepairOrder>` | Historia serwisowa pojazdu. |
| `findAvailableMechanics(date): List<Mechanic>` | Mechanicy dostępni danego dnia – do przydziału zlecenia. |

## RepairOrderServiceImpl

Implementacja `RepairOrderService`.

| Pole | Znaczenie |
|---|---|
| `orders: List<RepairOrder>` | Wszystkie zlecenia (asocjacja „przechowuje”). |
| `mechanics: List<Mechanic>` | Mechanicy warsztatu (asocjacja „przechowuje”). |

## Mechanic

Mechanik; dziedziczy po `User`. Po nim dziedziczy `WorkshopManager`, bo manager
ma też uprawnienia mechanika.

| Pole / metoda | Znaczenie |
|---|---|
| `absences: List<Date>` | Dni nieobecności. |
| `isAvailable(date): boolean` | Czy mechanik jest dostępny w danym dniu. |

## RepairOrder

Główny obiekt systemu. Powstaje z rezerwacji (asocjacja „powstaje z”, 0..1 do
1) i ma przydzielonego mechanika (0..* do 1). Pojazd i klient są dostępni przez
rezerwację.

| Pole | Znaczenie |
|---|---|
| `id` | Numer zlecenia. |
| `acceptanceDate` | Data przyjęcia pojazdu. |
| `mileage` | Przebieg przy przyjęciu. |
| `faultDescription` | Opis usterki po oględzinach. |
| `status` | Aktualny status (`RepairOrderStatus`). |
| `appointment` | Rezerwacja, z której powstało zlecenie. |
| `mechanic` | Przydzielony mechanik. |
| `repairs` | Wykonane naprawy (kompozycja, 1 do 0..*). |
| `statusHistory` | Historia zmian statusu (kompozycja, 1 do 1..*). |
| `costEstimates` | Kosztorysy dodatkowych prac (kompozycja, 1 do 0..*). |

| Metoda | Co robi |
|---|---|
| `RepairOrder(appointment, mileage, mechanic)` | Tworzy zlecenie ze statusem `ACCEPTED` i pierwszym wpisem w historii. |
| `assignMechanic(mechanic): void` | Zmienia przydzielonego mechanika. |
| `addRepair(repair): void` | Dopisuje wykonaną naprawę. |
| `addCostEstimate(estimate): void` | Dopisuje kosztorys i ustawia status `WAITING_FOR_ESTIMATE_APPROVAL`. |
| `decideOnEstimate(estimateId, accepted): void` | Zapisuje decyzję klienta dla wskazanego kosztorysu i przywraca status `IN_PROGRESS`. |
| `changeStatus(status, comment): void` | Zmienia status i dopisuje `StatusChange`. Odmawia przy statusie `SETTLED` oraz przejścia na `COMPLETED` bez żadnej naprawy lub z kosztorysem czekającym na decyzję. |
| `calculateCost(): double` | Suma kosztów napraw i zaakceptowanych kosztorysów. |

## RepairOrderStatus (typ wyliczeniowy)

| Wartość | Znaczenie |
|---|---|
| `ACCEPTED` | Przyjęte. |
| `IN_PROGRESS` | W trakcie realizacji. |
| `WAITING_FOR_PARTS` | Oczekuje na części. |
| `WAITING_FOR_ESTIMATE_APPROVAL` | Oczekuje na akceptację kosztorysu. |
| `COMPLETED` | Zakończone. |
| `SETTLED` | Rozliczone (wystawiono fakturę). |

## StatusChange

Jeden wpis historii statusów; istnieje tylko w ramach zlecenia (kompozycja).

| Pole / metoda | Znaczenie |
|---|---|
| `status` | Status ustawiony tą zmianą. |
| `changeDate` | Data zmiany. |
| `comment` | Komentarz mechanika widoczny dla klienta. |
| `StatusChange(status, comment)` | Tworzy wpis z bieżącą datą. |

## Repair

Pojedyncza wykonana czynność serwisowa w zleceniu.

| Pole | Znaczenie |
|---|---|
| `description` | Opis wykonanych czynności. |
| `workTime` | Czas pracy w godzinach. |
| `service` | Usługa z cennika (asocjacja „według usługi”, 0..* do 1). |
| `usedParts: List<PartItem>` | Użyte części z ilościami (kompozycja „zużywa”). |

| Metoda | Co robi |
|---|---|
| `Repair(service, description, workTime)` | Tworzy naprawę. |
| `addUsedPart(part, quantity): void` | Dopisuje użytą część z ilością. |
| `calculateCost(): double` | Cena usługi dla podanego czasu pracy plus koszt użytych części. |

## CostEstimate

Kosztorys usunięcia dodatkowej usterki, który klient akceptuje lub odrzuca.

| Pole | Znaczenie |
|---|---|
| `id` | Identyfikator – zlecenie może mieć kilka kosztorysów. |
| `faultDescription` | Opis dodatkowej usterki. |
| `services` | Potrzebne usługi z cennika (asocjacja „wycenia”, 0..* do 1..*). |
| `parts: List<PartItem>` | Potrzebne części z ilościami (kompozycja „wycenia”). |
| `status` | Stan decyzji (`EstimateStatus`). |

| Metoda | Co robi |
|---|---|
| `CostEstimate(faultDescription)` | Tworzy kosztorys ze statusem `PENDING`. |
| `addService(service)`, `addPart(part, quantity)` | Dodają pozycje do kosztorysu. |
| `calculateLaborCost()`, `calculatePartsCost()`, `calculateTotal()` | Koszt robocizny, koszt części i koszt łączny. |
| `accept()`, `reject()` | Zapisują decyzję klienta. |

## EstimateStatus (typ wyliczeniowy)

`PENDING` – czeka na decyzję klienta, `ACCEPTED` – zaakceptowany,
`REJECTED` – odrzucony.
