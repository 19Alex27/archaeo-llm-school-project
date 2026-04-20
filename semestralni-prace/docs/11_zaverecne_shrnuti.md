# 11. Závěrečné shrnutí

V této semestrální práci jsem vytvořil zjednodušený databázový model pro projekt Archaeo-LLM. Cílem nebylo vytvořit úplný profesionální archeologický informační systém, ale ukázat na konkrétním školním příkladu, jak lze archeologické nálezy, jejich popisné údaje, lokality, fotografie a materiály uspořádat do přehledné relační databáze.

* **1. Entity a vztahy tvoří základ databáze:**  
  Nejprve jsem určil hlavní části systému. Středem modelu je tabulka `artifact`, která představuje konkrétní archeologický nález. K ní jsou připojeny tabulky `category`, `site`, `photo`, `material`, `artifact_material` a `artifact_detail`. Díky tomu nejsou všechny údaje nahromaděny v jedné velké tabulce, ale jsou rozděleny podle svého významu.

* **2. E-R model pomáhá pochopit strukturu ještě před vytvořením databáze:**  
  Před samotným vytvořením SQL skriptu jsem pracoval s E-R diagramem v MySQL Workbench. Diagram mi umožnil vizuálně ověřit, která tabulka je hlavní, které tabulky ji doplňují a jaké vztahy mezi nimi vznikají. Stejná struktura je potom popsána také lineárním zápisem a relačním modelem dat.

* **3. Primární a cizí klíče zajišťují propojení tabulek:**  
  Každá hlavní tabulka má svůj primární klíč, například `artifact_id`, `category_id` nebo `site_id`. Cizí klíče umožňují propojit nález s kategorií, lokalitou, fotografiemi a dalšími údaji. Díky tomu se například název lokality nebo materiálu nemusí opisovat ke každému nálezu jako obyčejný text, ale databáze si související informace dohledá přes číselné identifikátory.

* **4. Vztah M:N je řešen samostatnou vazební tabulkou:**  
  Důležitou částí návrhu je tabulka `artifact_material`. Jeden archeologický nález může být popsán jedním nebo více materiály a jeden materiál se může vyskytovat u více nálezů. Proto tento vztah není řešen jedním textovým polem v tabulce `artifact`, ale samostatnou vazební tabulkou se složeným primárním klíčem `artifact_id` a `material_id`.

* **5. Normalizace pomáhá omezit duplicity a chyby:**  
  Při návrhu jsem dodržel základní principy první a druhé normální formy. Fotografie jsou odděleny do tabulky `photo`, materiály do tabulky `material`, lokality do tabulky `site` a detailní údaje do tabulky `artifact_detail`. Tím se snižuje riziko duplicit, nepřehlednosti a chyb při budoucích úpravách dat.

* **6. Databázový návrh je propojen s webovou prezentací:**  
  Součástí práce je také jednoduchá webová dokumentace projektu Archaeo-LLM publikovaná pomocí GitHub Pages. Webová stránka nepředstavuje plnohodnotnou databázovou aplikaci, ale slouží jako prezentační vrstva školního projektu. Ukázková data v práci jsou věcně sladěna s webovou prezentací a používají tři byzantské mince jako demonstrační příklady.

Výsledkem práce je ucelený školní model, který propojuje návrh relační databáze, E-R diagram, lineární zápis, relační model, normalizaci, demo data, SQL skript, phpMyAdmin a webovou prezentaci. Na tomto příkladu jsem si prakticky ověřil, že databázový návrh není jen teoretický obrázek, ale postup, který lze převést do skutečného databázového prostředí a srozumitelně představit uživateli.