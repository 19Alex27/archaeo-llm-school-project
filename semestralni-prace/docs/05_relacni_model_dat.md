# 5. Relační model dat a datový slovník

Navržené tabulky databáze Archaeo-LLM, jejich datové typy a použitá omezení atributů.

Databázový model je tvořen sedmi tabulkami: `category`, `site`, `artifact`, `photo`, `material`, `artifact_material` a `artifact_detail`. Hlavní tabulkou je `artifact`, která představuje evidovaný archeologický nález.

## Tabulka: category

Záznamy o kategoriích archeologických nálezů.

| Název | Datový typ | Modifikátory | Popis |
|---|---|---|---|
| category_id | INT | PK, NN, AI | Jednoznačný identifikátor kategorie |
| category_name | VARCHAR(100) | NN | Název kategorie archeologického nálezu |

## Tabulka: site

Záznamy o lokalitách, ke kterým jsou archeologické nálezy přiřazeny.

| Název | Datový typ | Modifikátory | Popis |
|---|---|---|---|
| site_id | INT | PK, NN, AI | Jednoznačný identifikátor lokality |
| site_name | VARCHAR(150) | NN | Název lokality |
| country | VARCHAR(200) | NN | Stát, ve kterém se lokalita nachází |
| region | VARCHAR(150) | NN | Region nebo oblast lokality |
| latitude | DECIMAL(9,6) | NN | Zeměpisná šířka lokality |
| longitude | DECIMAL(9,6) | NN | Zeměpisná délka lokality |
| description | VARCHAR(300) | NN | Stručný popis lokality |

## Tabulka: artifact

Hlavní tabulka databáze. Obsahuje základní evidenční údaje o archeologických nálezech.

| Název | Datový typ | Modifikátory | Popis |
|---|---|---|---|
| artifact_id | INT | PK, NN, AI | Jednoznačný identifikátor archeologického nálezu |
| category_id | INT | FK, NN | Odkaz na kategorii nálezu v tabulce `category` |
| site_id | INT | FK, NN | Odkaz na lokalitu nálezu v tabulce `site` |
| inventory_code | VARCHAR(50) | NN, UQ | Inventární nebo pracovní evidenční kód nálezu |
| title | VARCHAR(200) | NN | Název nebo pracovní označení nálezu |
| period_name | VARCHAR(100) | NN | Historické období, ke kterému je nález přiřazen |
| estimated_date | VARCHAR(100) | NN | Odhadované datování nálezu |
| short_description | VARCHAR(500) | NULL | Stručný popis nálezu |

## Tabulka: photo

Záznamy o fotografické dokumentaci archeologických nálezů.

| Název | Datový typ | Modifikátory | Popis |
|---|---|---|---|
| photo_id | INT | PK, NN, AI | Jednoznačný identifikátor fotografie |
| artifact_id | INT | FK, NN | Odkaz na archeologický nález v tabulce `artifact` |
| file_name | VARCHAR(255) | NN | Název souboru s fotografií |
| alt_text | VARCHAR(255) | NULL | Alternativní text pro webové zobrazení fotografie |
| is_primary | TINYINT | NN | Označení, zda jde o hlavní fotografii nálezu |

## Tabulka: material

Záznamy o materiálech, ze kterých mohou být archeologické nálezy vyrobeny.

| Název | Datový typ | Modifikátory | Popis |
|---|---|---|---|
| material_id | INT | PK, NN, AI | Jednoznačný identifikátor materiálu |
| material_name | VARCHAR(100) | NN | Název materiálu |
| description | VARCHAR(300) | NULL | Stručný popis materiálu |

## Tabulka: artifact_material

Vazební tabulka pro vztah M:N mezi archeologickými nálezy a materiály.

| Název | Datový typ | Modifikátory | Popis |
|---|---|---|---|
| artifact_id | INT | PK, FK, NN | Odkaz na archeologický nález v tabulce `artifact`; součást složeného primárního klíče |
| material_id | INT | PK, FK, NN | Odkaz na materiál v tabulce `material`; součást složeného primárního klíče |
| note | VARCHAR(200) | NULL | Poznámka ke konkrétnímu spojení nálezu a materiálu |

## Tabulka: artifact_detail

Rozšiřující tabulka s podrobnějšími měřitelnými a popisnými údaji o nálezu.

| Název | Datový typ | Modifikátory | Popis |
|---|---|---|---|
| artifact_id | INT | PK, FK, NN | Odkaz na archeologický nález v tabulce `artifact`; zároveň primární klíč detailního záznamu |
| weight_g | DECIMAL(10,2) | NULL | Hmotnost nálezu v gramech |
| height_mm | DECIMAL(10,2) | NULL | Výška nálezu v milimetrech |
| width_mm | DECIMAL(10,2) | NULL | Šířka nálezu v milimetrech |
| condition_state | VARCHAR(100) | NULL | Stav dochování nálezu |
| discovery_year | INT | NULL | Rok objevu nálezu |
| detailed_description | VARCHAR(1000) | NULL | Detailní textový popis nálezu |

### Legenda modifikátorů

* **PK** (Primary Key) - primární klíč: jednoznačný identifikátor záznamu v tabulce.
* **FK** (Foreign Key) - cizí klíč: odkaz na primární klíč v jiné tabulce, který vytváří relační vazbu.
* **UQ** (Unique) - unikátní omezení: hodnota se v daném sloupci nesmí opakovat.
* **NN** (Not Null) - hodnota nesmí být prázdná, je povinná.
* **AI** (Auto Increment) - automatické navyšování číselného identifikátoru databází.
* **NULL** - hodnota může zůstat prázdná, pokud údaj není znám nebo není povinný.

## Shrnutí relačního modelu

Relační model databáze Archaeo-LLM je postaven okolo tabulky `artifact`. Tato tabulka obsahuje hlavní evidenční údaje o nálezu a pomocí cizích klíčů odkazuje na kategorii a lokalitu.

Fotografie jsou odděleny do tabulky `photo`, materiály do tabulky `material` a vztah mezi nálezy a materiály je vyřešen pomocí vazební tabulky `artifact_material`. Detailní měřitelné a popisné údaje jsou uloženy v tabulce `artifact_detail`.

Díky tomuto rozdělení nejsou stejné údaje zbytečně opakovány v hlavní tabulce. Databáze je přehlednější, lépe rozšiřitelná a odpovídá základním principům relačního návrhu.

