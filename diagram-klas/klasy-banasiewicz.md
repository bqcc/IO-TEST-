# Klasy – Jakub Banasiewicz (obszar: Klient)

Obszar obejmuje konta użytkowników, pojazdy klienta i rezerwacje wizyt.
Powiązane przypadki użycia: *Dodaj pojazd*, *Zarezerwuj wizytę*.

Klasy: `ClientService`, `ClientServiceImpl`, `User`, `Client`, `Vehicle`,
`Appointment`, `AppointmentStatus`.

## ClientService (interfejs)

Punkt wejścia dla operacji klienta: rejestr pojazdów i rezerwacji.

| Metoda | Co robi |
|---|---|
| `addVehicle(client, vehicle): boolean` | Dodaje pojazd do konta klienta. Najpierw sprawdza numer VIN; zwraca `false`, gdy pojazd o tym VIN jest już zarejestrowany. |
| `findVehicleByVin(vin): Vehicle` | Szuka pojazdu o podanym VIN wśród pojazdów wszystkich klientów (kontrola unikalności VIN). |
| `findFreeSlots(date): List<Date>` | Zwraca wolne terminy wizyt w danym dniu – dane do kalendarza. |
| `addAppointment(appointment): void` | Zapisuje rezerwację i tym samym blokuje jej termin. |
| `findAppointmentsForDay(date): List<Appointment>` | Zwraca rezerwacje na dany dzień – lista, z której manager tworzy zlecenia. |

## ClientServiceImpl

Implementacja `ClientService` (relacja implementacji).

| Pole | Znaczenie |
|---|---|
| `clients: List<Client>` | Wszyscy zarejestrowani klienci (asocjacja „przechowuje”, 1 do 0..*). |
| `appointments: List<Appointment>` | Wszystkie rezerwacje wizyt (asocjacja „przechowuje”, 1 do 0..*). |

Metody są takie same jak w interfejsie.

## User (klasa abstrakcyjna)

Wspólna podstawa wszystkich kont: klienta, mechanika, magazyniera i managera.
Dziedziczą po niej `Client`, `Mechanic` i `Storekeeper`.

| Pole | Znaczenie |
|---|---|
| `id` | Identyfikator konta. |
| `login`, `password` | Dane logowania. |
| `firstName`, `lastName` | Imię i nazwisko – używane m.in. na fakturze i w szczegółach rezerwacji. |

| Metoda | Co robi |
|---|---|
| `logIn(login, password): boolean` | Sprawdza dane logowania. |
| `getFullName(): String` | Zwraca imię i nazwisko. |

## Client

Klient warsztatu, właściciel pojazdów. Dziedziczy po `User`.

| Pole | Znaczenie |
|---|---|
| `phone` | Telefon kontaktowy. |
| `vehicles: List<Vehicle>` | Pojazdy klienta (asocjacja „ma”, 1 do 0..*). |

| Metoda | Co robi |
|---|---|
| `addVehicle(vehicle): void` | Przypisuje pojazd do konta. |
| `getVehicles(): List<Vehicle>` | Zwraca listę pojazdów, np. do wyboru przy rezerwacji. |

## Vehicle

Pojazd klienta.

| Pole | Znaczenie |
|---|---|
| `vin` | Numer VIN (17 znaków, unikalny w systemie). |
| `make`, `model`, `productionYear` | Marka, model, rok produkcji. |
| `registrationNumber` | Numer rejestracyjny. |
| `mileage` | Aktualny przebieg. |

| Metoda | Co robi |
|---|---|
| `Vehicle(vin, make, model)` | Tworzy pojazd. |
| `updateMileage(mileage): void` | Aktualizuje przebieg przy przyjęciu pojazdu; nie pozwala wpisać wartości mniejszej niż ostatnia. |

## Appointment

Rezerwacja wizyty. Wskazuje klienta i pojazd (asocjacje „złożona przez” i
„dotyczy”, 0..* do 1), dzięki czemu ze zlecenia i faktury można dojść do
danych klienta.

| Pole | Znaczenie |
|---|---|
| `id` | Identyfikator rezerwacji. |
| `dateTime` | Termin wizyty. |
| `faultDescription` | Wstępny opis usterki wpisany przez klienta. |
| `status` | Status rezerwacji (`AppointmentStatus`). |
| `client`, `vehicle` | Kto rezerwuje i którego pojazdu dotyczy wizyta. |

| Metoda | Co robi |
|---|---|
| `Appointment(client, vehicle, dateTime)` | Tworzy rezerwację ze statusem `PENDING_CONFIRMATION`. |
| `confirm(): void` | Manager potwierdza rezerwację (`CONFIRMED`). |
| `markCompleted(): void` | Oznacza rezerwację jako zrealizowaną po utworzeniu zlecenia (`COMPLETED`). |

## AppointmentStatus (typ wyliczeniowy)

| Wartość | Znaczenie |
|---|---|
| `PENDING_CONFIRMATION` | Oczekuje na potwierdzenie. |
| `CONFIRMED` | Potwierdzona. |
| `COMPLETED` | Zrealizowana (powstało zlecenie naprawy). |
