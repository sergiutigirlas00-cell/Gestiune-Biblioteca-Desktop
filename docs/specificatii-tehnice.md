# Specificații tehnice — „Gestionarea unei biblioteci”

**Disciplina:** Planificarea aplicațiilor desktop · **Laborator 7**
**Student:** Țîgîrlaș Sergiu, grupa P-2522, CEITI

## 1. Scopul aplicației

Aplicație desktop pentru evidența fondului de carte al unei biblioteci și a împrumuturilor: catalogul cărților, înregistrarea cărților noi, eliberarea și returnarea exemplarelor, evidența cititorilor și rapoarte.

## 2. Actori

| Actor | Tip | Descriere | Cazuri de utilizare |
|---|---|---|---|
| Bibliotecar | principal | Gestionează catalogul, cititorii, împrumuturile și retururile. | UC-01 – UC-07 |
| Administrator | principal | Moștenește rolul Bibliotecarului (generalizare); în plus generează rapoarte. | UC-01 – UC-08 |
| Cititor | secundar | Primește și returnează cărți, are istoric de împrumuturi. | UC-06, UC-07, UC-09 |

## 3. Cerințe funcționale

| ID | Cerință | Caz de utilizare |
|---|---|---|
| CF-01 | Autentificarea utilizatorului (Bibliotecar / Administrator). | UC-01 |
| CF-02 | Afișarea catalogului de cărți cu paginare. | UC-02 |
| CF-03 | Căutare după titlu, autor sau ISBN și filtrare după gen, autor, an și disponibilitate. | UC-02 |
| CF-04 | Înregistrarea unei cărți noi într-o fereastră modală, cu validarea câmpurilor obligatorii și a ISBN-ului. | UC-03, UC-10 |
| CF-05 | Editarea și ștergerea unei cărți. | UC-04 |
| CF-06 | Înregistrarea unui împrumut: selectare cititor, selectare exemplar, termen de returnare (implicit 14 zile). | UC-06 |
| CF-07 | Verificarea disponibilității exemplarului înainte de împrumut. | UC-11 |
| CF-08 | Înregistrarea returului, cu calcularea automată a penalității pentru întârziere (5 MDL/zi). | UC-07, UC-12 |
| CF-09 | Lista împrumuturilor active, cu evidențierea celor întârziate. | UC-06, UC-07 |
| CF-10 | Gestionarea cititorilor și vizualizarea istoricului de împrumuturi. | UC-05, UC-09 |
| CF-11 | Generarea de rapoarte (PDF / Excel). | UC-08 |

## 4. Cerințe nefuncționale

- Platformă: Windows 10/11, .NET 8 (WPF), bază de date locală SQLite (opțional SQL Server).
- Timp de răspuns la căutare < 1 s pentru 10 000 de titluri.
- Interfață în limba română, rezoluție minimă 1280×800.
- Datele de autentificare sunt stocate hash-uit; roluri separate pentru Bibliotecar și Administrator.
- Copie de rezervă zilnică a bazei de date.

## 5. Ecranele prototipului (Figma)

| # | Ecran | Conținut | Tranziții |
|---|---|---|---|
| 01 | Catalogul cărților | Meniu lateral, statistici, zona de căutare și filtrare, tabelul cărților, paginare | „+ Carte nouă” → 02; „Împrumut / Retur”, „Împrumută” sau meniul „Împrumuturi și retururi” → 03 |
| 02 | Fereastra modală „Carte nouă” | Formular: titlu, autor, editura, ISBN, an, gen, nr. exemplare, raft, limba, descriere | „×”, „Anulează”, „Salvează cartea” → 01 |
| 03 | Înregistrare împrumut | Selectare cititor și exemplar, dată și termen, lista împrumuturilor active | Tab „Retur” sau „Returnează” → 04; „Anulează”, „Înregistrează împrumutul”, „Înapoi la catalog” → 01 |
| 04 | Înregistrare retur | Exemplar returnat, starea lui, penalitatea calculată | Tab „Împrumut” → 03; „Confirmă returul” → 01 |

## 6. Arhitectura

Arhitectură stratificată (3-tier) cu MVVM în stratul de prezentare. Vezi `diagrams/arhitectura-modulara.png` și secțiunea „Arhitectura modulară” din README. Diagrama cazurilor de utilizare este în `diagrams/uml-use-case.png`.

- **Biblioteca.UI**: Views și ViewModels (CatalogView, BookFormDialog, LoanReturnView, ReadersView, ReportsView).
- **Biblioteca.Services**: logica aplicației (BookService, LoanService, PenaltyService, ReaderService, ReportService).
- **Biblioteca.Data**: acces la date (repository-uri, LibraryDbContext cu EF Core, SQLite).
- **Biblioteca.Core**: modelele de domeniu (Carte, Exemplar, Cititor, Împrumut, Utilizator).
