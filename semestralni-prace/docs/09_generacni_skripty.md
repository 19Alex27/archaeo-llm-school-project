# 9. Generační skripty SQL v praxi

Kompletní SQL skript pro vytvoření databáze Archaeo-LLM je uložen ve složce:

`src/gene.sql`

Tento soubor byl vygenerován z databázového modelu vytvořeného v MySQL Workbench pomocí funkce Forward Engineer SQL CREATE Script. Úkolem skriptu je převést vizuální E-R model do konkrétních SQL příkazů, které vytvoří databázové schéma, tabulky, primární klíče, cizí klíče, indexy a vazby mezi tabulkami.

Pro import do lokálního prostředí XAMPP/phpMyAdmin byla použita také upravená kopie:

`src/gene_mariadb.sql`

Důvodem je kompatibilita mezi MySQL Workbench a MariaDB. Původní export obsahoval u indexů vlastnost `VISIBLE`, kterou použitý server MariaDB v prostředí XAMPP nepřijal. Proto byla vytvořena kompatibilní verze bez této vlastnosti. Databázová logika, tabulky a vazby tím zůstávají stejné.

## Vytvoření databázového schématu

Na začátku skriptu je vytvořeno databázové schéma `archaeo_llm_db`. Následně příkaz `USE` určí, že všechny další tabulky budou vytvářeny právě v této databázi.

```sql
CREATE SCHEMA IF NOT EXISTS `archaeo_llm_db` DEFAULT CHARACTER SET utf8;
USE `archaeo_llm_db`;
```

Tím se připraví samostatný prostor pro databázi projektu Archaeo-LLM. V tomto prostoru budou uloženy všechny tabulky související s evidencí archeologických nálezů.

## Založení jednoduché číselníkové tabulky category

Tabulka `category` slouží jako číselník kategorií archeologických nálezů. Obsahuje pouze identifikátor a název kategorie. Jedná se o jednoduchou tabulku bez cizího klíče.

```sql
CREATE TABLE IF NOT EXISTS `archaeo_llm_db`.`category` (
  `category_id` INT NOT NULL AUTO_INCREMENT,
  `category_name` VARCHAR(100) NOT NULL,
  PRIMARY KEY (`category_id`))
ENGINE = InnoDB;
```

Sloupec `category_id` je primární klíč. Díky vlastnosti `AUTO_INCREMENT` databáze automaticky přidělí nové číslo každé nově vložené kategorii. Sloupec `category_name` je povinný, protože obsahuje omezení `NOT NULL`. Kategorie tedy nemůže být založena bez názvu.

## Založení tabulky site

Tabulka `site` eviduje lokality, ke kterým jsou archeologické nálezy přiřazeny. Oproti tabulce `category` obsahuje více popisných atributů: název lokality, stát, region, souřadnice a stručný popis.

```sql
CREATE TABLE IF NOT EXISTS `archaeo_llm_db`.`site` (
  `site_id` INT NOT NULL AUTO_INCREMENT,
  `site_name` VARCHAR(150) NOT NULL,
  `country` VARCHAR(200) NOT NULL,
  `region` VARCHAR(150) NOT NULL,
  `latitude` DECIMAL(9,6) NOT NULL,
  `longitude` DECIMAL(9,6) NOT NULL,
  `description` VARCHAR(300) NOT NULL,
  PRIMARY KEY (`site_id`))
ENGINE = InnoDB;
```

Zeměpisná šířka a délka jsou uloženy jako `DECIMAL(9,6)`. To znamená, že hodnota může obsahovat celkem devět číslic, z toho šest za desetinnou čárkou. Tento typ je vhodnější než běžný text, protože souřadnice jsou číselné údaje.

## Založení hlavní tabulky artifact

Nejdůležitější tabulkou celé databáze je `artifact`. Tato tabulka představuje konkrétní archeologický nález a zároveň propojuje základní evidence s kategorií a lokalitou.

```sql
CREATE TABLE IF NOT EXISTS `archaeo_llm_db`.`artifact` (
  `artifact_id` INT NOT NULL AUTO_INCREMENT,
  `category_id` INT NOT NULL,
  `site_id` INT NOT NULL,
  `inventory_code` VARCHAR(50) NOT NULL,
  `title` VARCHAR(200) NOT NULL,
  `period_name` VARCHAR(100) NOT NULL,
  `estimated_date` VARCHAR(100) NOT NULL,
  `short_description` VARCHAR(500) NULL,
  PRIMARY KEY (`artifact_id`),
  UNIQUE INDEX `inventory_code_UNIQUE` (`inventory_code` ASC),
  INDEX `fk_artifact_category_idx` (`category_id` ASC),
  INDEX `fk_artifact_site_idx` (`site_id` ASC),
  CONSTRAINT `fk_artifact_category`
    FOREIGN KEY (`category_id`)
    REFERENCES `archaeo_llm_db`.`category` (`category_id`)
    ON DELETE RESTRICT
    ON UPDATE CASCADE,
  CONSTRAINT `fk_artifact_site`
    FOREIGN KEY (`site_id`)
    REFERENCES `archaeo_llm_db`.`site` (`site_id`)
    ON DELETE RESTRICT
    ON UPDATE CASCADE)
ENGINE = InnoDB;
```

Sloupec `artifact_id` je primární klíč nálezu. Sloupce `category_id` a `site_id` jsou cizí klíče. To znamená, že nález neukládá přímo text kategorie ani text lokality. Místo toho se odkazuje na odpovídající záznamy v tabulkách `category` a `site`.

Důležitý je také sloupec `inventory_code`. Ten má unikátní index `inventory_code_UNIQUE`. Díky tomu se stejný inventární nebo pracovní evidenční kód nemůže v tabulce opakovat.

Omezení `ON DELETE RESTRICT` zabraňuje smazání kategorie nebo lokality, pokud na ni stále odkazuje nějaký nález. Omezení `ON UPDATE CASCADE` zajistí, že při změně identifikátoru v rodičovské tabulce se změna promítne i do navázaných záznamů.

## Založení tabulky photo

Fotografie nejsou ukládány přímo do tabulky `artifact`, protože jeden archeologický nález může mít více fotografií. Proto existuje samostatná tabulka `photo`.

```sql
CREATE TABLE IF NOT EXISTS `archaeo_llm_db`.`photo` (
  `photo_id` INT NOT NULL AUTO_INCREMENT,
  `artifact_id` INT NOT NULL,
  `file_name` VARCHAR(255) NOT NULL,
  `alt_text` VARCHAR(255) NULL,
  `is_primary` TINYINT(1) NOT NULL,
  PRIMARY KEY (`photo_id`),
  INDEX `fk_photo_artifact_idx` (`artifact_id` ASC),
  CONSTRAINT `fk_photo_artifact`
    FOREIGN KEY (`artifact_id`)
    REFERENCES `archaeo_llm_db`.`artifact` (`artifact_id`)
    ON DELETE RESTRICT
    ON UPDATE CASCADE)
ENGINE = InnoDB;
```

Cizí klíč `artifact_id` říká, ke kterému nálezu fotografie patří. Tím vzniká vztah 1:N mezi tabulkami `artifact` a `photo`: jeden nález může mít více fotografií, ale každá fotografie patří právě k jednomu nálezu.

Sloupec `is_primary` je typu `TINYINT(1)` a slouží k označení hlavní fotografie. Hodnota `1` může znamenat hlavní fotografii a hodnota `0` doplňkovou fotografii.

## Založení tabulky material

Tabulka `material` obsahuje seznam materiálů používaných při popisu archeologických nálezů.

```sql
CREATE TABLE IF NOT EXISTS `archaeo_llm_db`.`material` (
  `material_id` INT NOT NULL AUTO_INCREMENT,
  `material_name` VARCHAR(100) NOT NULL,
  `description` VARCHAR(300) NULL,
  PRIMARY KEY (`material_id`),
  UNIQUE INDEX `material_name_UNIQUE` (`material_name` ASC))
ENGINE = InnoDB;
```

Sloupec `material_name` má unikátní index. To znamená, že název stejného materiálu by se neměl v číselníku opakovat vícekrát. Například materiál `Bronz` má být uložen jednou a jednotlivé nálezy se na něj mají pouze odkazovat.

## Přemostění vztahu M:N pomocí artifact_material

Mezi archeologickými nálezy a materiály může existovat vztah M:N. Jeden nález může obsahovat více materiálů a jeden materiál se může vyskytovat u více nálezů. Takový vztah nelze správně vyřešit jedním cizím klíčem v tabulce `artifact`.

Proto je vytvořena vazební tabulka `artifact_material`.

```sql
CREATE TABLE IF NOT EXISTS `archaeo_llm_db`.`artifact_material` (
  `artifact_id` INT NOT NULL,
  `material_id` INT NOT NULL,
  `note` VARCHAR(200) NULL,
  PRIMARY KEY (`artifact_id`, `material_id`),
  INDEX `fk_artifactmaterial_material_idx` (`material_id` ASC),
  CONSTRAINT `fk_artifactmaterial_artifact`
    FOREIGN KEY (`artifact_id`)
    REFERENCES `archaeo_llm_db`.`artifact` (`artifact_id`)
    ON DELETE RESTRICT
    ON UPDATE CASCADE,
  CONSTRAINT `fk_artifactmaterial_material`
    FOREIGN KEY (`material_id`)
    REFERENCES `archaeo_llm_db`.`material` (`material_id`)
    ON DELETE RESTRICT
    ON UPDATE CASCADE)
ENGINE = InnoDB;
```

Primární klíč je zde složený ze dvou sloupců: `artifact_id` a `material_id`. Tím je zajištěno, že stejná kombinace nálezu a materiálu nemůže být zapsána dvakrát.

Sloupec `note` je nepovinný. Umožňuje doplnit poznámku ke konkrétnímu spojení nálezu a materiálu, například že daný materiál je hlavní, doplňkový nebo pouze stopově zachovaný.

## Rozšiřující tabulka artifact_detail

Tabulka `artifact_detail` slouží k uložení podrobnějších údajů o nálezu. Neobsahuje vlastní samostatný identifikátor, ale používá `artifact_id` jako primární i cizí klíč.

```sql
CREATE TABLE IF NOT EXISTS `archaeo_llm_db`.`artifact_detail` (
  `artifact_id` INT NOT NULL,
  `weight_g` DECIMAL(10,2) NULL,
  `height_mm` DECIMAL(10,2) NULL,
  `width_mm` DECIMAL(10,2) NULL,
  `condition_state` VARCHAR(100) NULL,
  `discovery_year` INT NULL,
  `detailed_description` VARCHAR(1000) NULL,
  PRIMARY KEY (`artifact_id`),
  CONSTRAINT `fk_artifactdetail_artifact`
    FOREIGN KEY (`artifact_id`)
    REFERENCES `archaeo_llm_db`.`artifact` (`artifact_id`)
    ON DELETE RESTRICT
    ON UPDATE CASCADE)
ENGINE = InnoDB;
```

Tento zápis vyjadřuje vztah mezi hlavním záznamem a jeho detailem. Detailní údaje nemohou existovat bez odpovídajícího nálezu v tabulce `artifact`. Proto je `artifact_id` zároveň primárním klíčem detailní tabulky i cizím klíčem směřujícím do tabulky `artifact`.

Hmotnost a rozměry jsou uloženy jako `DECIMAL(10,2)`, protože jde o měřitelné číselné hodnoty s přesností na dvě desetinná místa. Ostatní údaje, například stav dochování a detailní popis, jsou textové.

## Shrnutí SQL skriptu

Generační SQL skript převádí databázový návrh Archaeo-LLM do skutečných příkazů jazyka SQL. Vytváří databázové schéma `archaeo_llm_db` a sedm tabulek: `category`, `site`, `artifact`, `photo`, `material`, `artifact_material` a `artifact_detail`.

Skript zároveň definuje primární klíče, cizí klíče, unikátní omezení a indexy. Nejvýznamnější je tabulka `artifact`, která tvoří střed databáze. Tabulka `artifact_material` řeší vztah M:N mezi nálezy a materiály a tabulka `artifact_detail` rozšiřuje hlavní záznam nálezu o podrobnější údaje.

Díky tomu je databázový model nejen popsán v E-R diagramu a relačním modelu, ale také prakticky realizovatelný v prostředí MySQL nebo MariaDB.