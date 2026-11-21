# Car Service – system obsługi warsztatu samochodowego

Projekt z przedmiotu Inżynieria Programowania.

Autorzy: Jakub Banasiewicz, Łukasz Bera, Kacper Bednarz, Dawid Woźniak

## Raport

- [Raport (opis systemu, słownik, user stories, diagram przypadków użycia)](https://bqcc.github.io/IO-TEST-/raport.html)

## Scenariusze przypadków użycia

Złączone: [scenariusze.html](https://bqcc.github.io/IO-TEST-/scenariusze/scenariusze.html)

| Aktor | Autor | Scenariusze |
|---|---|---|
| Klient | Jakub Banasiewicz | [scenariusze-klient.html](https://bqcc.github.io/IO-TEST-/scenariusze/scenariusze-klient.html) |
| Mechanik | Łukasz Bera | [scenariusze-mechanik.html](https://bqcc.github.io/IO-TEST-/scenariusze/scenariusze-mechanik.html) |
| Magazynier | Kacper Bednarz | [scenariusze-magazynier.html](https://bqcc.github.io/IO-TEST-/scenariusze/scenariusze-magazynier.html) |
| Manager warsztatu | Dawid Woźniak | [scenariusze-manager.html](https://bqcc.github.io/IO-TEST-/scenariusze/scenariusze-manager.html) |

## Diagramy aktywności

| Aktor | Autor | Diagramy |
|---|---|---|
| Klient | Jakub Banasiewicz | [diagramy-klient.html](https://bqcc.github.io/IO-TEST-/diagramy/diagramy-klient.html) |
| Mechanik | Łukasz Bera | [diagramy-mechanik.html](https://bqcc.github.io/IO-TEST-/diagramy/diagramy-mechanik.html) |
| Magazynier | Kacper Bednarz | [diagramy-magazynier.html](https://bqcc.github.io/IO-TEST-/diagramy/diagramy-magazynier.html) |
| Manager warsztatu | Dawid Woźniak | [diagramy-manager.html](https://bqcc.github.io/IO-TEST-/diagramy/diagramy-manager.html) |

## Diagram klas

- [diagram-klas.html](https://bqcc.github.io/IO-TEST-/diagram-klas/diagram-klas.html)

Odpowiedzialność za klasy:

| Obszar | Autor | Klasy | Opis klas |
|---|---|---|---|
| Klient | Jakub Banasiewicz | `ClientService`, `ClientServiceImpl`, `User`, `Client`, `Vehicle`, `Appointment`, `AppointmentStatus` | [klasy-banasiewicz.md](diagram-klas/klasy-banasiewicz.md) |
| Mechanik | Łukasz Bera | `RepairOrderService`, `RepairOrderServiceImpl`, `Mechanic`, `RepairOrder`, `RepairOrderStatus`, `StatusChange`, `Repair`, `CostEstimate`, `EstimateStatus` | [klasy-bera.md](diagram-klas/klasy-bera.md) |
| Magazynier | Kacper Bednarz | `WarehouseService`, `WarehouseServiceImpl`, `Storekeeper`, `SparePart`, `PartItem`, `PartRequest`, `PartRequestStatus`, `Supplier`, `SupplierOrder`, `SupplierOrderStatus` | [klasy-bednarz.md](diagram-klas/klasy-bednarz.md) |
| Manager warsztatu | Dawid Woźniak | `BillingService`, `BillingServiceImpl`, `WorkshopManager`, `PriceList`, `ServiceItem`, `Invoice`, `PaymentMethod`, `Report` | [klasy-wozniak.md](diagram-klas/klasy-wozniak.md) |

## Wzorce projektowe

| Aktor | Autor | Wzorzec | Plik |
|---|---|---|---|
| Klient | Jakub Banasiewicz | Prototyp | [wzorce-klient.html](https://bqcc.github.io/IO-TEST-/wzorce/wzorce-klient.html) |
| Mechanik | Łukasz Bera | Budowniczy | [wzorce-mechanik.html](https://bqcc.github.io/IO-TEST-/wzorce/wzorce-mechanik.html) |
| Magazynier | Kacper Bednarz | Metoda fabrykująca | [wzorce-magazynier.html](https://bqcc.github.io/IO-TEST-/wzorce/wzorce-magazynier.html) |
| Manager warsztatu | Dawid Woźniak | Singleton | [wzorce-manager.html](https://bqcc.github.io/IO-TEST-/wzorce/wzorce-manager.html) |
