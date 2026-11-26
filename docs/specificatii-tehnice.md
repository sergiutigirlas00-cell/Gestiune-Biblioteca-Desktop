# Specificații tehnice — „Gestionarea unei biblioteci”

**Disciplina:** Planificarea aplicațiilor desktop · **Laborator 7**
**Student:** Țîgîrlaș Sergiu, grupa P-2522, CEITI

## 1. Scopul aplicației

Aplicație desktop pentru evidența fondului de carte al unei biblioteci și a împrumuturilor: catalogul cărților, înregistrarea cărților noi, eliberarea și returnarea exemplarelor, evidența cititorilor și rapoarte.

## 2. Actori

| Actor | Descriere |
|---|---|
| Bibliotecar | Utilizatorul principal: gestionează catalogul, cititorii, împrumuturile și retururile. |
| Administrator | Moștenește drepturile bibliotecarului, generează rapoarte și gestionează conturile. |
| Cititor | Actor secundar: primește și returnează cărți, are istoric de împrumuturi. |

## 3. Cerințe funcționale

| ID | Cerință |
|---|---|
| CF-01 | Autentificarea utilizatorului (Bibliotecar / Administrator). |
| CF-02 | Afișarea catalogului de cărți cu paginare. |
| CF-03 | Căutare după titlu, autor sau ISBN și filtrare după gen, autor, an și disponibilitate. |
| CF-04 | Înregistrarea unei cărți noi într-o fereastră modală, cu validarea câmpurilor obligatorii și a ISBN-ului. |
| CF-05 | Editarea și ștergerea unei cărți. |
| CF-06 | Înregistrarea unui împrumut: selectare cititor, selectare exemplar, termen de returnare (implicit 14 zile). |
| CF-07 | Verificarea disponibilității exemplarului înainte de împrumut. |
| CF-08 | Înregistrarea returului, cu calcularea automată a penalității pentru întârziere (5 MDL/zi). |
| CF-09 | Lista împrumuturilor active, cu evidențierea celor întârziate. |
| CF-10 | Gestionarea cititorilor și vizualizarea istoricului de împrumuturi. |
| CF-11 | Generarea de rapoarte (PDF / Excel). |

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

Arhitectură stratificată (3-tier) cu MVVM în stratul de prezentare. Vezi `diagrams/arhitectura-modulara.png` și secțiunea „Arhitectura” din README.

- **Biblioteca.UI**: Views și ViewModels (CatalogView, BookFormDialog, LoanReturnView, ReadersView, ReportsView).
- **Biblioteca.Services**: logica aplicației (BookService, LoanService, PenaltyService, ReaderService, ReportService).
- **Biblioteca.Data**: acces la date (repository-uri, LibraryDbContext cu EF Core, SQLite).
- **Biblioteca.Core**: modelele de domeniu (Carte, Exemplar, Cititor, Împrumut, Utilizator).
