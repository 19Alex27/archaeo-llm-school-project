# 7. Tabulky s demo daty

Nyní si ukážeme, jak by se navržená databáze Archaeo-LLM začala plnit konkrétními záznamy. V předchozích kapitolách jsme popsali tabulky, klíče a vztahy spíše teoreticky. V této kapitole už bude vidět, jak se jednotlivé tabulky propojují pomocí číselných identifikátorů.

Demo data jsou záměrně sladěna s webovou prezentací projektu. Jako ukázkové záznamy jsou použity tři byzantské mince z veřejně dostupné nálezové databáze Portable Antiquities Scheme. Nejde o úplný odborný katalog, ale o školní demonstrační datovou sadu, která ukazuje, jak lze archeologické nálezy, lokality, fotografie, materiály a detailní údaje uložit do relační databáze.

Důležité je pochopit, že databáze není jedna velká tabulka podobná poznámkovému sešitu. Každý typ informace má své vlastní místo. Kategorie nálezu je uložena v tabulce `category`, lokalita v tabulce `site`, samotný archeologický předmět v tabulce `artifact`, fotografie v tabulce `photo`, materiály v tabulce `material` a spojení mezi nálezem a materiálem v tabulce `artifact_material`.

## `category` (Tabulka kategorií nálezů)

Nejdříve založíme kategorie, které umožní typologické zařazení numismatických nálezů. V ukázkových datech pracujeme pouze s mincemi, ale číselník kategorií může být později rozšířen i o jiné typy archeologických předmětů.

| category_id | category_name | Vysvětlení / komentář |
|---:|---|---|
| **1** | Mince | Obecná kategorie pro numismatické archeologické nálezy. |
| **2** | Byzantský follis | Kategorie pro byzantské měděné mince typu follis. |
| **3** | Anonymní byzantský follis | Kategorie pro anonymní byzantské follisy, u kterých je určení spojeno s obdobím vlády panovníka. |

*Všimněte si, že v tabulce `category` není žádný konkrétní nález. Jsou zde pouze typy nálezů. Teprve tabulka `artifact` později řekne, který konkrétní předmět do které kategorie patří.*

## `site` (Tabulka lokalit)

Druhou základní tabulkou je `site`. Zde se evidují lokality nebo oblasti, ke kterým mohou být archeologické nálezy přiřazeny. Lokalitu zapisujeme jen jednou a jednotlivé nálezy se na ni potom odkazují pomocí `site_id`.

U veřejných archeologických databází nemusí být přesná poloha nálezu vždy plně zveřejněna. V ukázkových datech proto rozlišujeme doložené veřejné souřadnice a technické demonstrační hodnoty. Pokud přesná souřadnice není ve veřejném náhledu doložena, je v tabulce uvedena hodnota `0.000000` jako technická zástupná hodnota školního modelu, nikoli jako skutečná poloha nálezu.

| site_id | site_name | country | region | latitude | longitude | description |
|---:|---|---|---|---:|---:|---|
| **1** | Stainforth | Spojené království | Yorkshire and the Humber | 53.591921 | -1.064776 | Lokalita v oblasti Doncasteru; veřejně doložené souřadnice z nálezového záznamu. |
| **2** | South Staffordshire | Spojené království | West Midlands / Staffordshire | 0.000000 | 0.000000 | Veřejný záznam uvádí oblast nálezu; přesné souřadnice nejsou v ukázce doloženy. |
| **3** | Saxilby with Ingleby | Spojené království | East Midlands / Lincolnshire | 0.000000 | 0.000000 | Veřejný záznam uvádí farnost nebo oblast nálezu; přesné souřadnice nejsou v ukázce doloženy. |

*První lokalita má veřejně doložené souřadnice. U druhé a třetí lokality je důležité nepředstírat přesnost, která ve veřejném podkladu není doložena. Proto je v demo datech použita technická zástupná hodnota a skutečnost je vysvětlena v popisu.*

## `material` (Tabulka materiálů)

Podobně jako u kategorií a lokalit si předem založíme také materiály. Materiál je samostatná tabulka proto, aby se názvy jako slitina mědi nebo bronz nemusely stále opisovat do každého záznamu nálezu.

| material_id | material_name | description |
|---:|---|---|
| **1** | Slitina mědi | Kovový materiál používaný u drobných a středních byzantských mincí. |
| **2** | Měď | Základní kov, který může být součástí mincovních slitin. |
| **3** | Bronz | Slitina mědi používaná u mincí a dalších archeologických předmětů. |

*Tabulka `material` zatím neříká, který nález je z určitého materiálu. Uchovává pouze seznam možných materiálů. Skutečné přiřazení materiálu ke konkrétnímu nálezu řeší až vazební tabulka `artifact_material`.*

## `artifact` (Hlavní tabulka archeologických nálezů)

Tabulka `artifact` je hlavním středem celé databáze. Zde už zakládáme konkrétní archeologické předměty. Každý záznam obsahuje vlastní identifikátor nálezu a zároveň cizí klíče `category_id` a `site_id`.

Díky tomu databáze ví, do jaké kategorie nález patří a z jaké lokality pochází, aniž by bylo nutné tyto textové údaje stále opakovat.

| artifact_id | category_id | site_id | inventory_code | title | period_name | estimated_date | short_description |
|---:|---:|---:|---|---|---|---|---|
| **1** | **1** | **1** | PAS-1228223 | Byzantská měděná mince | Byzantské období | cca 600–700 n. l. | Silně opotřebená měděná mince, pravděpodobně byzantský nummus nebo follis. Na rubu je patrné velké písmeno M. |
| **2** | **3** | **2** | PAS-1225300 | Anonymní byzantský follis Romana III. Argyra | Byzantské období | 1028–1034 n. l. | Opotřebený anonymní byzantský follis z období vlády Romana III. Argyra. Na líci je patrná hlava s křížem. |
| **3** | **2** | **3** | PAS-1112809 | Byzantský měděný follis Justiniána I. | Byzantské období | cca 550–560 n. l. | Měděný byzantský follis císaře Justiniána I., ražený v Konstantinopoli a později provrtaný pro zavěšení. |

*Podívejme se na první řádek. Záznam `PAS-1228223` má `category_id = 1`, takže podle tabulky `category` víme, že jde o minci. Má také `site_id = 1`, takže podle tabulky `site` víme, že je přiřazen k lokalitě Stainforth. Slova „Mince“ a „Stainforth“ se tedy neopisují přímo do hlavní tabulky. Databáze si je dohledá přes číselné klíče.*

*Druhý řádek má `category_id = 3`, tedy anonymní byzantský follis, a `site_id = 2`, tedy South Staffordshire. Třetí řádek má `category_id = 2`, tedy byzantský follis, a `site_id = 3`, tedy Saxilby with Ingleby.*

## `photo` (Tabulka fotografií)

Archeologický nález může mít jednu nebo více fotografií. U mincí je obrazová dokumentace důležitá, protože umožňuje porovnávat líc, rub, opis, symboly a celkový stav dochování. Proto fotografie nejsou ukládány přímo do tabulky `artifact`, ale mají vlastní tabulku `photo`.

| photo_id | artifact_id | file_name | alt_text | is_primary |
|---:|---:|---|---|---:|
| **1** | **1** | pas_1228223_coin.jpg | Byzantská měděná mince z lokality Stainforth | 1 |
| **2** | **2** | pas_1225300_coin.jpg | Anonymní byzantský follis Romana III. Argyra | 1 |
| **3** | **3** | pas_1112809_coin.jpg | Byzantský měděný follis Justiniána I. | 1 |

*V ukázkových datech má každý nález jednu hlavní fotografii. Databázový model ale umožňuje přidat k jednomu nálezu více fotografií, například zvlášť líc a rub mince. Sloupec `is_primary` označuje, která fotografie je hlavní.*

## `artifact_material` (Vazební tabulka mezi nálezem a materiálem)

Mezi archeologickými nálezy a materiály může vzniknout vztah M:N. Jeden nález může být popsán jedním nebo více materiály a jeden materiál se může opakovat u mnoha různých nálezů. Takový vztah nelze správně uložit pouze jedním sloupcem do tabulky `artifact`.

Proto vzniká vazební tabulka `artifact_material`. Jejím úkolem je spojovat konkrétní nález s konkrétním materiálem.

| artifact_id | material_id | note | Vysvětlení ukázkové vazby |
|---:|---:|---|---|
| **1** | **1** | Hlavní materiál mince. | Nález 1 je propojen s materiálem 1, tedy se slitinou mědi. |
| **2** | **1** | Hlavní materiál mince. | Nález 2 je propojen s materiálem 1, tedy se slitinou mědi. |
| **3** | **1** | Hlavní materiál mince. | Nález 3 je propojen s materiálem 1, tedy se slitinou mědi. |

*Všimněte si, že stejný materiál může být použit u více různých nálezů. Název materiálu se neopisuje do tabulky `artifact`, ale je uložen pouze jednou v tabulce `material`. Vazební tabulka potom uchovává pouze číselné propojení.*

*Pokud by byl některý předmět složen z více materiálů, přidaly by se pro stejný `artifact_id` další řádky s jinými hodnotami `material_id`. Právě to je hlavní smysl vazební tabulky.*

## `artifact_detail` (Detailní údaje o nálezu)

Tabulka `artifact_detail` rozšiřuje hlavní záznam v tabulce `artifact` o podrobnější údaje. Tyto údaje nejsou vždy nutné pro rychlý přehled, ale jsou důležité pro odbornější popis nálezu.

| artifact_id | weight_g | height_mm | width_mm | condition_state | discovery_year | detailed_description |
|---:|---:|---:|---:|---|---:|---|
| **1** | 4.25 | 20.80 | 19.11 | Silně opotřebená, špatně zachovaná | 2024 | Mince je silně opotřebená. Identifikace není zcela jistá, ale ikonografie odpovídá pozdně antickému nebo byzantskému okruhu. |
| **2** | 10.20 | 30.80 | 30.80 | Opotřebená, ale kompletní | `NULL` | Anonymní byzantský follis z období Romana III. Argyra. Mince je opotřebená, ale základní motivy jsou stále patrné. |
| **3** | 19.19 | 30.00 | 30.00 | Kompletní, provrtaná pro zavěšení | 2007 | Follis Justiniána I. s velkým písmenem M na rubu. Mince je kompletní a nese otvor po dodatečném zavěšení. |

*Zde je důležité, že `artifact_id` slouží zároveň jako odkaz na hlavní tabulku `artifact`. Detailní záznam s `artifact_id = 1` tedy patří ke stejnému nálezu, který je v tabulce `artifact` uveden jako byzantská měděná mince.*

*Tím je hlavní tabulka `artifact` udržena přehledná. Obsahuje základní evidenční informace, zatímco měřitelné a podrobné údaje jsou odděleny v tabulce `artifact_detail`.*

## Jak se demo data propojí dohromady

Nyní si můžeme přečíst celý první ukázkový nález přes více tabulek:

1. V tabulce `artifact` najdeme záznam `artifact_id = 1`.
2. Tento záznam má `inventory_code = PAS-1228223`.
3. Má `category_id = 1`, což v tabulce `category` znamená kategorii Mince.
4. Má také `site_id = 1`, což v tabulce `site` znamená lokalitu Stainforth.
5. V tabulce `photo` najdeme fotografii se stejným `artifact_id = 1`.
6. V tabulce `artifact_material` najdeme spojení `artifact_id = 1` a `material_id = 1`.
7. V tabulce `material` zjistíme, že `material_id = 1` znamená Slitina mědi.
8. V tabulce `artifact_detail` najdeme rozměry, hmotnost, stav dochování a detailní popis tohoto nálezu.

Výsledkem je jeden srozumitelný evidenční celek, ale technicky je rozložený do více tabulek. To je hlavní princip relační databáze: informace nejsou chaoticky nahromaděné v jednom místě, ale jsou rozdělené podle významu a propojené pomocí klíčů.

## Shrnutí demo dat

Demo data ukazují, že databáze Archaeo-LLM dokáže evidovat tři byzantské mince, jejich kategorie, lokality, fotografie, materiálové složení a detailní údaje. Hlavní tabulkou je `artifact`, ale sama o sobě by nestačila. Teprve propojení s ostatními tabulkami vytváří úplný popis nálezu.

Tento návrh zároveň ukazuje praktický smysl normalizace. Kategorie, lokality a materiály nejsou zbytečně opisovány do každého řádku. Místo toho jsou uloženy jednou a ostatní tabulky se na ně odkazují přes primární a cizí klíče.