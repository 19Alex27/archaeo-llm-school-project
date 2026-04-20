# 6. Normalizace databáze

Při návrhu databáze Archaeo-LLM byla dodržena základní pravidla první a druhé normální formy. Účelem normalizace je udržet data přehledná, omezit duplicity a zabránit situacím, kdy by se stejná informace musela ručně přepisovat na více místech.

V našem případě nejde o velký profesionální archeologický informační systém, ale o školní databázový model. Přesto je důležité, aby měl správnou logiku: archeologický nález, lokalita, fotografie, materiál a detailní údaje nejsou smíchány v jedné obrovské tabulce, ale jsou rozděleny podle významu.

## První normální forma (1.NF)

První normální forma požaduje, aby jednotlivé hodnoty v tabulkách byly atomické, tedy aby jedna buňka neobsahovala více různých údajů najednou.

Konkrétně by bylo chybou uložit například všechny fotografie jednoho nálezu do jednoho textového pole v tabulce `artifact`, například ve formě `coin_001_front.jpg, coin_001_back.jpg`. Takový zápis by byl nepřehledný a špatně zpracovatelný. Proto byla vytvořena samostatná tabulka `photo`, kde má každá fotografie vlastní řádek a pomocí cizího klíče `artifact_id` je přiřazena ke konkrétnímu nálezu.

Podobný problém by vznikl u materiálů. Archeologický předmět může být popsán jedním nebo více materiály. Pokud bychom materiály zapisovali přímo do tabulky `artifact` jako text typu `bronz, stříbro`, porušili bychom princip jedné hodnoty v jedné buňce. Proto je seznam materiálů uložen v samostatné tabulce `material` a spojení mezi nálezem a materiálem řeší vazební tabulka `artifact_material`.

Tím je vztah mezi nálezy a materiály řešen čistě databázově. Jeden nález může mít více materiálů a jeden materiál se může vyskytovat u více nálezů, aniž by bylo nutné vytvářet nepřehledné textové seznamy.

První normální forma je dodržena také u lokality. Tabulka `site` neukládá polohu jako jeden neurčitý text, ale odděluje název lokality, stát, region, zeměpisnou šířku, zeměpisnou délku a popis. Díky tomu lze s těmito údaji pracovat samostatně.

V rámci přísného výkladu 1.NF lze uvést vědomý kompromis u atributu `estimated_date` v tabulce `artifact`. Datování archeologického nálezu může být v praxi přibližné, například `6. století` nebo `5.-6. století`. Proto je tento údaj uložen jako textový atribut `VARCHAR(100)`. Pro účely školního demonstračního modelu je tento kompromis přijatelný, protože lépe odpovídá povaze archeologického datování.

## Druhá normální forma (2.NF)

Druhá normální forma požaduje, aby každý neklíčový atribut závisel na celém primárním klíči příslušné tabulky. U tabulek s jednoduchým primárním klíčem je toto pravidlo splněno přímo: například `category_name` závisí na `category_id`, údaje o lokalitě závisí na `site_id` a základní popis nálezu závisí na `artifact_id`.

Nejdůležitější místo pro posouzení 2.NF je v našem modelu tabulka `artifact_material`. Tato tabulka má složený primární klíč tvořený dvojicí `artifact_id` a `material_id`. Atribut `note` nepopisuje pouze samotný nález ani pouze samotný materiál. Popisuje právě konkrétní spojení jednoho nálezu s jedním materiálem. Proto je závislý na celém složeném klíči.

Kdyby byl například název materiálu uložen přímo v tabulce `artifact_material`, docházelo by k duplicitám. Název `Bronz` by se opakoval u každého bronzového předmětu. Správné řešení je uložit název materiálu pouze jednou v tabulce `material` a v ostatních tabulkách používat pouze číselný odkaz `material_id`.

Stejná logika platí i pro lokality a kategorie. Název lokality, stát, region a souřadnice nejsou opisovány ke každému archeologickému nálezu. Jsou uloženy v tabulce `site` a tabulka `artifact` obsahuje pouze cizí klíč `site_id`. Kategorie nálezu je také uložena samostatně v tabulce `category` a v hlavní tabulce nálezů se používá pouze odkaz `category_id`.

Díky tomuto návrhu se při změně údajů upravuje jen jedno místo. Pokud by bylo potřeba opravit popis lokality, stačí změnit jeden záznam v tabulce `site`. Není nutné přepisovat všechny archeologické nálezy z této lokality. Podobně při úpravě popisu materiálu se mění pouze tabulka `material`, zatímco vazby na konkrétní nálezy zůstávají zachovány.

Navržený model tak omezuje aktualizační anomálie, mazací anomálie i zbytečné opakování dat. Hlavní tabulka `artifact` zůstává přehledná a ostatní tabulky ji doplňují podle svého významu.

## Shrnutí normalizace

Databáze Archaeo-LLM splňuje základní principy první a druhé normální formy. Údaje jsou rozděleny do samostatných tabulek, opakující se informace jsou nahrazeny odkazy pomocí cizích klíčů a vztah M:N mezi archeologickými nálezy a materiály je řešen samostatnou vazební tabulkou `artifact_material`.

Model je záměrně přiměřeně jednoduchý, ale zachovává správnou relační logiku. Právě proto je vhodný jako školní demonstrační databáze pro projekt Archaeo-LLM.
