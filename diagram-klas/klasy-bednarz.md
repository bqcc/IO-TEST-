# Klasy – Kacper Bednarz (obszar: Magazynier)

Obszar obejmuje magazyn części, zamówienia części od mechaników oraz
zamówienia u dostawców. Powiązane przypadki użycia: *Sprawdź dostępność
części*, *Wydaj części mechanikowi*, *Zamów części u dostawcy*, *Przyjmij
dostawę części*.

Klasy: `WarehouseService`, `WarehouseServiceImpl`, `Storekeeper`, `SparePart`,
`PartItem`, `PartRequest`, `PartRequestStatus`, `Supplier`, `SupplierOrder`,
`SupplierOrderStatus`.

## WarehouseService (interfejs)

| Metoda | Co robi |
|---|---|
| `findParts(query): List<SparePart>` | Wyszukuje części po nazwie lub numerze katalogowym (katalog mechanika i sprawdzanie dostępności). |
| `addPartRequest(request): void` | Przyjmuje zamówienie części od mechanika. |
| `findPendingRequests(): List<PartRequest>` | Lista niezrealizowanych zamówień części. |
| `issueParts(requestId): void` | Wydaje części: zmniejsza stany, oznacza zamówienie jako zrealizowane. Przy braku części oznacza je jako oczekujące, a gdy zlecenie jest już zakończone – anuluje. |
| `findIssuedParts(order): List<PartItem>` | Części wydane do zlecenia – lista widoczna w formularzu naprawy. |
| `findSuppliers(part): List<Supplier>` | Dostawcy oferujący daną część. |
| `addSupplierOrder(order): void` | Zapisuje zamówienie u dostawcy i je wysyła. |
| `receiveDelivery(supplierOrderId, quantity): void` | Rejestruje dostawę: zwiększa stan magazynowy i oznacza oczekujące zamówienia mechaników jako gotowe do wydania. |

## WarehouseServiceImpl

Implementacja `WarehouseService`. Przechowuje (asocjacje „przechowuje”, 1 do
0..*): `parts` – katalog części, `partRequests` – zamówienia mechaników,
`suppliers` – dostawców, `supplierOrders` – zamówienia u dostawców.

## Storekeeper

Magazynier; dziedziczy po `User`. Nie ma własnych pól ani metod – korzysta z
operacji `WarehouseService`.

## SparePart

Część zamienna w katalogu magazynu.

| Pole | Znaczenie |
|---|---|
| `catalogNumber`, `name`, `manufacturer` | Numer katalogowy, nazwa, producent. |
| `price` | Cena jednostkowa. |
| `stockQuantity` | Stan magazynowy. |
| `reservedQuantity` | Ilość zarezerwowana dla zleceń. |

| Metoda | Co robi |
|---|---|
| `isAvailable(quantity): boolean` | Czy wolna ilość (stan minus rezerwacje) wystarcza. |
| `reserve(quantity): void` | Rezerwuje ilość dla zlecenia. |
| `decreaseStock(quantity): void` | Zmniejsza stan przy wydaniu. |
| `increaseStock(quantity): void` | Zwiększa stan przy dostawie. |

## PartItem

Pozycja „część + ilość”. Jedna klasa używana w trzech miejscach: w zamówieniu
części (`PartRequest`), w naprawie (`Repair` – części użyte) i w kosztorysie
(`CostEstimate`). Każda pozycja należy do jednego właściciela (kompozycja) i
wskazuje część z katalogu (asocjacja „dotyczy”, 0..* do 1).

| Metoda | Co robi |
|---|---|
| `PartItem(part, quantity)` | Tworzy pozycję. |
| `reduceQuantity(quantity): void` | Zmniejsza ilość – używane przy częściowym wydaniu, gdy brakująca ilość jest usuwana z zamówienia. |
| `calculateCost(): double` | Cena części razy ilość. |

## PartRequest

Zamówienie części złożone przez mechanika do konkretnego zlecenia (asocjacja
„dotyczy”, 0..* do 1).

| Pole | Znaczenie |
|---|---|
| `id` | Identyfikator zamówienia. |
| `repairOrder` | Zlecenie, do którego zamawiane są części. |
| `items: List<PartItem>` | Pozycje zamówienia (kompozycja, 1 do 1..*). |
| `status` | Stan zamówienia (`PartRequestStatus`). |

| Metoda | Co robi |
|---|---|
| `PartRequest(repairOrder)` | Tworzy zamówienie ze statusem `PENDING`. |
| `addItem(part, quantity): void` | Dodaje pozycję; odrzuca część już zamówioną do tego zlecenia i jeszcze niewydaną. |
| `markWaiting()` | Brak części w magazynie – zamówienie czeka. |
| `markReadyForIssue()` | Części dotarły w dostawie – można wydawać. |
| `markFulfilled()` | Części wydane mechanikowi. |
| `cancel()` | Anulowanie, gdy zlecenie zostało w międzyczasie zakończone lub rozliczone. |

## PartRequestStatus (typ wyliczeniowy)

`PENDING` – złożone, `WAITING` – oczekuje na części, `READY_FOR_ISSUE` – gotowe
do wydania, `FULFILLED` – zrealizowane, `CANCELLED` – anulowane.

## Supplier

Zewnętrzny dostawca części.

| Pole / metoda | Znaczenie |
|---|---|
| `name` | Nazwa dostawcy. |
| `minimumOrderQuantity` | Minimalna ilość zamówienia. |
| `offeredParts` | Części w ofercie (asocjacja „oferuje”, 0..* do 0..*). |
| `offers(part): boolean` | Czy dostawca oferuje daną część. |

## SupplierOrder

Zamówienie jednej części u dostawcy. Samo pamięta, ile już dostarczono, więc
osobna klasa dostawy nie jest potrzebna.

| Pole | Znaczenie |
|---|---|
| `id` | Identyfikator zamówienia. |
| `supplier`, `part` | U kogo i co zamówiono (asocjacje 0..* do 1). |
| `quantity` | Ilość zamówiona. |
| `deliveredQuantity` | Ilość dostarczona do tej pory. |
| `price`, `expectedDeliveryDate` | Cena i przewidywany termin dostawy (podsumowanie zamówienia). |
| `status` | Stan zamówienia (`SupplierOrderStatus`). |

| Metoda | Co robi |
|---|---|
| `SupplierOrder(supplier, part, quantity)` | Tworzy zamówienie; ilość musi być co najmniej minimalna u dostawcy. |
| `send(): boolean` | Wysyła zamówienie do dostawcy. Przy niepowodzeniu ustawia `TO_RESEND`. |
| `registerDelivery(quantity): void` | Dolicza dostarczoną ilość i ustawia `PARTIALLY_COMPLETED` albo `COMPLETED`. |
| `getMissingQuantity(): int` | Ile jeszcze brakuje do pełnej dostawy. |

## SupplierOrderStatus (typ wyliczeniowy)

`SENT` – wysłane, `TO_RESEND` – do ponownej wysyłki, `PARTIALLY_COMPLETED` –
częściowo zrealizowane, `COMPLETED` – zrealizowane.
