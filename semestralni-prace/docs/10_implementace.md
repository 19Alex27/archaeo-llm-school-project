# 10. Implementace

Tato kapitola popisuje praktický postup, který byl použit při realizaci databázového a webového projektu Archaeo-LLM. Nejde pouze o teoretický návrh tabulek, ale také o ověření, že navržené databázové schéma lze vytvořit v reálném databázovém prostředí a následně představit formou jednoduché webové dokumentace.

Implementace projektu probíhala ve dvou navazujících částech. První část se týkala návrhu a vytvoření relační databáze v nástrojích MySQL Workbench a phpMyAdmin. Druhá část se týkala webové prezentace projektu, která slouží jako dokumentační vrstva a ukazuje databázový návrh uživateli ve srozumitelné podobě.

## 1. Návrh schématu v MySQL Workbench

Jako hlavní nástroj pro návrh databázového schématu byl použit **MySQL Workbench**. Tento program umožňuje navrhovat databázi vizuálně pomocí E-R diagramu. Díky tomu je možné ještě před vytvořením databáze vidět jednotlivé tabulky, jejich atributy a vztahy mezi nimi.

V rámci modelu Archaeo-LLM bylo vytvořeno sedm tabulek:

- `category`,
- `site`,
- `artifact`,
- `photo`,
- `material`,
- `artifact_material`,
- `artifact_detail`.

Hlavní tabulkou je `artifact`, která představuje konkrétní archeologický nález. K ní jsou pomocí cizích klíčů připojeny tabulky kategorií, lokalit, fotografií, materiálů a detailních údajů. V modelu byly nastaveny primární klíče, cizí klíče, datové typy, povinné hodnoty a základní indexy.

Postup v MySQL Workbench:

1. Vytvoření nového databázového modelu.
2. Přidání E-R diagramu.
3. Vložení tabulek podle navrženého relačního modelu.
4. Nastavení sloupců, datových typů a modifikátorů.
5. Vytvoření vztahů mezi tabulkami pomocí cizích klíčů.
6. Kontrola, zda diagram odpovídá lineárnímu zápisu a relačnímu modelu dat.

Výsledný E-R model odpovídá kapitolám 3, 4 a 5 této práce. To znamená, že stejné tabulky a vztahy jsou popsány slovně, zapsány lineárně, uvedeny v datovém slovníku a zároveň zobrazeny v diagramu.

## 2. Lokální databázové prostředí XAMPP a phpMyAdmin

Pro praktické vytvoření a kontrolu databáze bylo použito lokální prostředí **XAMPP** s nástrojem **phpMyAdmin**. XAMPP slouží jako jednoduchý balíček pro školní a vývojové použití. Obsahuje komponenty potřebné pro lokální běh databázového a webového prostředí.

Použité části prostředí:

- **Apache** – lokální webový server,
- **MariaDB / MySQL** – databázový server,
- **PHP** – skriptovací jazyk potřebný pro běh phpMyAdminu,
- **phpMyAdmin** – webové rozhraní pro správu databází.

phpMyAdmin byl dostupný v prohlížeči přes lokální adresu:

`http://localhost/phpmyadmin`

V tomto prostředí byla vytvořena databáze `archaeo_llm_db`. Následně bylo možné přes phpMyAdmin zobrazit její tabulky, strukturu hlavní tabulky `artifact` a také relační schéma mezi tabulkami.

## 3. Export SQL skriptu z MySQL Workbench

Po dokončení E-R modelu byl v MySQL Workbench vygenerován SQL skript pomocí funkce **Forward Engineer SQL CREATE Script**. Tato funkce převádí vizuální databázový model do příkazů jazyka SQL.

Výsledný soubor byl uložen jako:

`src/gene.sql`

Tento soubor obsahuje příkazy pro vytvoření databázového schématu `archaeo_llm_db`, všech sedmi tabulek, primárních klíčů, cizích klíčů, indexů a unikátních omezení.

Protože export z MySQL Workbench obsahoval u některých indexů vlastnost `VISIBLE`, byla pro import do prostředí XAMPP/phpMyAdmin připravena také kompatibilní kopie:

`src/gene_mariadb.sql`

Tato upravená verze neobsahuje vlastnost `VISIBLE`, kterou použitá MariaDB v prostředí XAMPP nepřijala. Logika databáze se tím nemění. Tabulky, klíče a vazby zůstávají stejné.

## 4. Import databáze do phpMyAdmin

Pro vytvoření databáze v lokálním prostředí byl použit import SQL souboru přes phpMyAdmin.

Postup importu:

1. Spuštění XAMPP a kontrola běhu databázového serveru.
2. Otevření phpMyAdminu v prohlížeči.
3. Vytvoření nebo výběr databáze `archaeo_llm_db`.
4. Přechod na záložku **Import**.
5. Výběr souboru `src/gene_mariadb.sql`.
6. Spuštění importu.
7. Kontrola, že byly vytvořeny všechny tabulky.

Po importu byly v databázi viditelné tabulky:

- `artifact`,
- `artifact_detail`,
- `artifact_material`,
- `category`,
- `material`,
- `photo`,
- `site`.

Tím bylo ověřeno, že SQL skript odpovídá navrženému databázovému modelu.

## 5. Ověření struktury databáze

Po importu byla databáze zkontrolována v phpMyAdmin. Kontrola probíhala ve třech úrovních.

První kontrolou byl seznam tabulek databáze `archaeo_llm_db`. Ten potvrdil, že databáze obsahuje všech sedm navržených tabulek.

Druhou kontrolou byla struktura hlavní tabulky `artifact`. V této tabulce byly ověřeny hlavní sloupce:

- `artifact_id`,
- `category_id`,
- `site_id`,
- `inventory_code`,
- `title`,
- `period_name`,
- `estimated_date`,
- `short_description`.

Zvlášť důležitý je sloupec `inventory_code`, který má unikátní omezení. Díky tomu se jeden inventární nebo pracovní evidenční kód nemůže v databázi opakovat.

Třetí kontrolou bylo zobrazení relačních vazeb v phpMyAdminu. V návrháři relací bylo možné ověřit, že tabulka `artifact` je propojena s tabulkami `category`, `site`, `photo`, `artifact_material` a `artifact_detail`. Vazební tabulka `artifact_material` je dále propojena s tabulkou `material`.

## 6. Webová dokumentace projektu

Součástí projektu není pouze databázové schéma, ale také jednoduchá webová prezentace Archaeo-LLM. Tato webová část neslouží jako plnohodnotná databázová aplikace s dynamickým napojením na MySQL. Jejím účelem je dokumentovat projekt a představit jeho obsah uživateli ve webové podobě.

Webová prezentace je publikována pomocí GitHub Pages na adrese:

`https://19alex27.github.io/archaeo-llm-school-project/`

Web obsahuje základní části školní prezentace projektu:

- hlavní stránku,
- stránku databáze,
- galerii,
- stránku o projektu,
- kontaktní stránku,
- navigační menu,
- hlavičku a patičku,
- tabulku,
- formulář,
- obrázky,
- externí odkaz.

Webová prezentace tak doplňuje databázovou část práce. Databáze popisuje strukturu dat a vztahy mezi nimi, zatímco web ukazuje, jak lze projekt představit běžnému uživateli. Ukázková data v této dokumentaci jsou věcně sladěna s webovou prezentací: jako demonstrační příklady jsou použity tři byzantské mince.

## 7. Rozsah implementace a omezení

Implementace je záměrně přizpůsobena rozsahu školní semestrální práce. Cílem nebylo vytvořit profesionální archeologický informační systém ani plnohodnotnou webovou aplikaci s přihlášením uživatelů a dynamickou správou dat.

Cílem bylo vytvořit přehledný a ověřitelný model, který ukazuje:

- návrh relační databáze,
- práci s entitami a atributy,
- použití primárních a cizích klíčů,
- řešení vztahu M:N pomocí vazební tabulky,
- vytvoření SQL skriptu,
- import databáze do phpMyAdminu,
- základní webovou dokumentaci projektu.

Tento rozsah odpovídá demonstračnímu charakteru práce. Databázový návrh je dostatečně jednoduchý pro pochopení, ale zároveň obsahuje reálné databázové principy používané i ve větších systémech.

## Shrnutí implementace

Implementace projektu Archaeo-LLM prokázala, že navržený databázový model lze převést z E-R diagramu do skutečného SQL skriptu a následně vytvořit v lokálním databázovém prostředí.

MySQL Workbench byl použit pro návrh a export databázového modelu. XAMPP a phpMyAdmin byly použity pro import, kontrolu tabulek a ověření vztahů. Webová prezentace na GitHub Pages doplňuje práci o veřejně dostupnou dokumentační vrstvu.

Výsledkem je školní projekt, který spojuje databázový návrh, praktickou implementaci a webovou dokumentaci do jednoho srozumitelného celku.