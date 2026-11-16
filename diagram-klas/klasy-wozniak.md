# Klasy – Dawid Woźniak (obszar: Manager warsztatu)

Obszar obejmuje cennik usług, faktury i raporty. Powiązane przypadki użycia:
*Dodaj usługę do cennika*, *Wystaw fakturę*, generowanie raportów.

Klasy: `BillingService`, `BillingServiceImpl`, `WorkshopManager`, `PriceList`,
`ServiceItem`, `Invoice`, `PaymentMethod`, `Report`.

Tworzenie zlecenia z rezerwacji i przydział mechanika, które też wykonuje
manager, korzystają z klas `RepairOrder` i `RepairOrderService`
(opisane w [klasy-bera.md](klasy-bera.md)) oraz `Appointment`
([klasy-banasiewicz.md](klasy-banasiewicz.md)).

## BillingService (interfejs)

| Metoda | Co robi |
|---|---|
| `getPriceList(): PriceList` | Zwraca cennik usług – do przeglądania i dodawania usług. |
| `issueInvoice(order, method, taxId): Invoice` | Wystawia fakturę z kolejnym numerem dla zakończonego zlecenia i zmienia jego status na rozliczone. Odmawia, gdy pozycja zlecenia nie ma ceny. |
| `generateReport(periodStart, periodEnd): Report` | Tworzy raport z pracy warsztatu za podany okres. |

## BillingServiceImpl

Implementacja `BillingService`.

| Pole | Znaczenie |
|---|---|
| `priceList` | Cennik usług (asocjacja „zarządza”, 1 do 1). |
| `invoices` | Wystawione faktury (asocjacja „przechowuje”, 1 do 0..*). |
| `orderService` | Dostęp do zleceń potrzebnych do raportu (asocjacja „korzysta z”). |

Zależność „tworzy” do `Report`: raport powstaje w `generateReport` i nie jest
przechowywany.

## WorkshopManager

Manager warsztatu; dziedziczy po `Mechanic`, więc ma też uprawnienia mechanika.
Nie ma własnych pól ani metod – korzysta z operacji serwisów.

## PriceList

Cennik usług; składa się z usług (agregacja „składa się z”, 1 do 0..*).

| Metoda | Co robi |
|---|---|
| `addService(service): boolean` | Dodaje usługę; zwraca `false`, gdy usługa o tej nazwie już istnieje. |
| `findService(name): ServiceItem` | Szuka usługi po nazwie. |
| `getServices(): List<ServiceItem>` | Zwraca wszystkie usługi – lista w formularzu naprawy i kosztorysu. |

## ServiceItem

Pozycja cennika – usługa serwisowa.

| Pole | Znaczenie |
|---|---|
| `name`, `description` | Nazwa i opis usługi. |
| `rate` | Stawka za roboczogodzinę albo cena stała. |
| `fixedPrice` | `true`, gdy `rate` jest ceną stałą. |
| `estimatedTime` | Szacowany czas wykonania. |
| `validFrom` | Data, od której obowiązuje stawka. |

| Metoda | Co robi |
|---|---|
| `ServiceItem(name, rate, fixedPrice)` | Tworzy usługę; stawka musi być dodatnia. |
| `calculatePrice(workTime): double` | Cena stała albo stawka razy czas pracy. |

## Invoice

Faktura za zlecenie (asocjacja „dotyczy”, 0..1 do 1). Dane klienta pochodzą ze
zlecenia: zlecenie → rezerwacja → klient.

| Pole | Znaczenie |
|---|---|
| `number` | Kolejny numer faktury. |
| `issueDate` | Data wystawienia. |
| `repairOrder` | Rozliczane zlecenie. |
| `paymentMethod` | Forma płatności (`PaymentMethod`). |
| `taxId` | Opcjonalny NIP firmy klienta. |

| Metoda | Co robi |
|---|---|
| `Invoice(number, repairOrder)` | Tworzy fakturę dla zlecenia. |
| `calculateLaborCost(): double` | Koszt robocizny według cennika. |
| `calculatePartsCost(): double` | Koszt użytych części. |
| `calculateTotal(): double` | Kwota łączna. |

## PaymentMethod (typ wyliczeniowy)

`CASH` – gotówka, `CARD` – karta, `TRANSFER` – przelew.

## Report

Zestawienie z pracy warsztatu za wybrany okres.

| Pole / metoda | Znaczenie |
|---|---|
| `periodStart`, `periodEnd` | Okres raportu. |
| `completedOrders` | Liczba zrealizowanych zleceń. |
| `usedParts` | Liczba zużytych części. |
| `revenue` | Przychód. |
| `Report(periodStart, periodEnd)` | Tworzy raport dla okresu. |
