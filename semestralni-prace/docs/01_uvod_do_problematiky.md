# 1. Úvod do problematiky

Představme si, že potřebujeme evidovat několik archeologických nálezů, například tři byzantské mince z veřejné nálezové databáze. Na úplném začátku by nám pravděpodobně stačila jednoduchá tabulka v Excelu nebo textový dokument. Do řádků bychom si zapsali název předmětu, přibližné datování, materiál, místo nálezu a poznámku.

Problém nastává ve chvíli, kdy se taková evidence začne rozrůstat. Jeden archeologický nález může mít více fotografií, může být přiřazen ke konkrétní lokalitě, patřit do určité kategorie a zároveň být popsán jedním nebo více materiály. Pokud by se všechny tyto údaje zapisovaly do jedné velké tabulky, rychle by vznikaly duplicity, nepřehlednost a chyby při úpravách dat.

Typickým příkladem je lokalita nebo materiál. Pokud by se název stejné lokality opisoval u desítek nálezů, při opravě názvu nebo doplnění popisu by bylo nutné upravovat mnoho řádků. Podobně by se mohly stále opakovat názvy materiálů, například slitina mědi nebo bronz. Takový způsob evidence je pro větší množství záznamů nepraktický.

Právě zde nastupuje relační databáze. Jejím smyslem je rozdělit informace do samostatných tabulek podle jejich významu a propojit je pomocí primárních a cizích klíčů. Díky tomu není nutné opakovat stejné textové údaje na více místech. Databáze si související informace dohledá pomocí číselných identifikátorů.

V této semestrální práci je řešen zjednodušený databázový model pro projekt Archaeo-LLM. Cílem není vytvořit úplný profesionální archeologický informační systém, ale přehledně ukázat základní principy návrhu databáze pro evidenci archeologických nálezů a jejich webovou dokumentaci.

Návrh databáze je postaven na sedmi hlavních tabulkách: `category`, `site`, `artifact`, `photo`, `material`, `artifact_material` a `artifact_detail`. Tyto tabulky společně umožňují evidovat archeologické předměty, jejich zařazení, místo nálezu, fotografie, materiálové složení a detailní popisné údaje.

Hlavní tabulkou je `artifact`, která reprezentuje konkrétní archeologický nález. Ostatní tabulky ji doplňují: `category` určuje typ nálezu, `site` popisuje lokalitu, `photo` uchovává obrazovou dokumentaci, `material` eviduje materiály, `artifact_material` řeší jejich vazbu na nálezy a `artifact_detail` ukládá podrobnější měřitelné údaje.

Součástí projektu je také jednoduchá webová prezentace Archaeo-LLM publikovaná prostřednictvím GitHub Pages. Webová část neslouží jako plnohodnotná databázová aplikace, ale jako dokumentační a prezentační vrstva školního projektu. Ukazuje, jak lze návrh databáze a její obsah představit uživateli ve srozumitelné podobě.

Tato práce tedy propojuje dvě oblasti: návrh relační databáze a základní webovou dokumentaci. Výsledkem je školní model, na kterém lze vysvětlit entity, atributy, vztahy, normalizaci, ukázková data, SQL skripty a praktickou implementaci.
