# 2. Slovní vyjádření zadání

V rámci návrhu databáze projektu Archaeo-LLM jsou evidovány archeologické nálezy a údaje, které s nimi bezprostředně souvisejí. Cílem není vytvořit profesionální archeologický informační systém, ale školní relační model, na kterém lze srozumitelně ukázat práci s entitami, atributy, primárními klíči, cizími klíči a vztahy mezi tabulkami.

Hlavní sledovanou entitou je archeologický nález. K němu se dále vážou kategorie, lokalita, fotografie, materiálové složení a detailní popisné údaje. Přesné datové typy a databázová omezení jsou rozepsány až v kapitole relačního modelu dat. V této kapitole je popsána věcná logika zadání.

## 1. Kategorie nálezu (category)

Kategorie slouží k základnímu typologickému zařazení archeologických předmětů. Díky samostatné tabulce kategorií není nutné opakovat název kategorie u každého nálezu jako obyčejný text.

* Jednoznačný identifikátor kategorie.
* Název kategorie nálezu.

*Vztah k nálezu:* Jedna kategorie může být přiřazena k více archeologickým nálezům. Jeden konkrétní nález je v tomto zjednodušeném modelu zařazen právě do jedné kategorie. Jedná se tedy o vztah typu 1:N mezi tabulkami `category` a `artifact`.

## 2. Lokalita nálezu (site)

Lokalita představuje místo, oblast nebo geografický kontext, ke kterému je archeologický nález přiřazen. Samostatná tabulka lokalit umožňuje ukládat údaje o místě pouze jednou a následně na ně odkazovat z tabulky nálezů.

* Jednoznačný identifikátor lokality.
* Název lokality.
* Stát.
* Region nebo oblast.
* Zeměpisná šířka.
* Zeměpisná délka.
* Stručný popis lokality.

*Vztah k nálezu:* Jedna lokalita může být spojena s více archeologickými nálezy. Každý nález je v modelu přiřazen k jedné lokalitě. Jedná se o vztah typu 1:N mezi tabulkami `site` a `artifact`.

## 3. Archeologický nález (artifact)

Archeologický nález je hlavní entitou celé databáze. Tato tabulka soustřeďuje základní evidenční informace o předmětu a zároveň propojuje ostatní části modelu.

* Jednoznačný identifikátor nálezu.
* Odkaz na kategorii nálezu.
* Odkaz na lokalitu nálezu.
* Inventární nebo pracovní evidenční kód.
* Název nebo pracovní označení předmětu.
* Historické období.
* Odhadované datování.
* Stručný popis předmětu.

*Vztahy:* Každý nález patří do jedné kategorie a je přiřazen k jedné lokalitě. Jeden nález může mít více fotografií. Jeden nález může být propojen s jedním nebo více materiály. Jeden nález může mít také rozšiřující detailní záznam s měřitelnými a popisnými údaji.

## 4. Fotografie nálezu (photo)

Fotografie slouží k obrazové dokumentaci archeologických předmětů. Fotografie nejsou ukládány přímo do hlavní tabulky nálezů, protože jeden nález může mít více snímků, například přední a zadní stranu mince.

* Jednoznačný identifikátor fotografie.
* Odkaz na archeologický nález.
* Název souboru s fotografií.
* Alternativní text pro webové zobrazení.
* Označení, zda jde o hlavní fotografii.

*Vztah k nálezu:* Jeden archeologický nález může mít více fotografií. Každá fotografie patří právě k jednomu nálezu. Jedná se o vztah typu 1:N mezi tabulkami `artifact` a `photo`.

## 5. Materiál (material)

Materiálová tabulka obsahuje seznam materiálů, ze kterých mohou být archeologické předměty vyrobeny nebo které se při jejich popisu používají. Samostatná tabulka materiálů zabraňuje tomu, aby se názvy materiálů opakovaly přímo u každého nálezu.

* Jednoznačný identifikátor materiálu.
* Název materiálu.
* Stručný popis materiálu.

*Vztah k nálezu:* Jeden materiál se může vyskytovat u více archeologických nálezů. Jeden archeologický nález může být zároveň propojen s více materiály. Proto mezi tabulkami `artifact` a `material` vzniká vztah M:N, který je řešen pomocí vazební tabulky `artifact_material`.

## 6. Vazba nález - materiál (artifact_material)

Tabulka `artifact_material` je vazební tabulka. Jejím úkolem je spojovat konkrétní archeologický nález s konkrétním materiálem. Tato tabulka je nutná proto, že vztah mezi nálezy a materiály není jednoduchý vztah 1:N, ale vztah M:N.

* Odkaz na archeologický nález.
* Odkaz na materiál.
* Volitelná poznámka k materiálovému složení.

*Vztahová logika:* Kombinace `artifact_id` a `material_id` tvoří složený primární klíč. Díky tomu nelze stejné spojení nálezu a materiálu zapsat duplicitně. Tabulka zároveň umožňuje, aby jeden nález měl více materiálů a jeden materiál byl použit u více různých nálezů.

## 7. Detailní údaje o nálezu (artifact_detail)

Tabulka `artifact_detail` rozšiřuje hlavní evidenční záznam o podrobnější údaje. Tyto údaje nejsou nezbytné pro základní identifikaci nálezu, ale jsou důležité pro přesnější popis předmětu.

* Odkaz na archeologický nález.
* Hmotnost v gramech.
* Výška v milimetrech.
* Šířka v milimetrech.
* Stav dochování.
* Rok objevu.
* Detailní textový popis.

*Vztah k nálezu:* Detailní záznam patří vždy ke konkrétnímu archeologickému nálezu. V modelu je vazba řešena pomocí sdíleného identifikátoru `artifact_id`, který je v tabulce `artifact_detail` zároveň primárním i cizím klíčem. Tím je vyjádřeno, že detailní údaje pouze rozšiřují hlavní záznam v tabulce `artifact`.

## Shrnutí vztahů

Databázový model Archaeo-LLM je postaven okolo tabulky `artifact`. Ta propojuje archeologický nález s kategorií, lokalitou, fotografiemi, materiály a detailními údaji.

Model obsahuje vztahy 1:N mezi kategorií a nálezy, lokalitou a nálezy, nálezem a fotografiemi. Vztah M:N mezi nálezy a materiály je řešen vazební tabulkou `artifact_material`. Detailní údaje jsou odděleny do tabulky `artifact_detail`, která rozšiřuje hlavní záznam nálezu.
