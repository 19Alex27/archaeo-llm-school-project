# Archaeo-LLM: databáze archeologických nálezů a webová dokumentace projektu

- Oleksii Syvochka
- duben 2026
- V2I, VOŠ, SPŠ a SOŠ, Varnsdorf, p. o.

## Úvod

> Tento repozitář obsahuje semestrální práci zaměřenou na návrh relační databáze pro evidenci archeologických nálezů a na webovou dokumentaci projektu Archaeo-LLM.

> Cílem práce je ukázat, jak lze archeologické předměty, jejich kategorie, lokality, fotografie, materiály a detailní popisné údaje uspořádat do přehledného databázového modelu a následně je představit formou jednoduché webové prezentace.

## Obsah práce

1. [Úvod do problematiky](docs/01_uvod_do_problematiky.md)
2. [Slovní vyjádření zadání](docs/02_slovni_vyjadreni.md)
3. [E-R model](docs/03_er_diagram.md)
4. [Lineární zápis struktury](docs/04_linearni_zapis_struktury.md)
5. [Relační model dat a datový slovník](docs/05_relacni_model_dat.md)
6. [Normalizace databáze](docs/06_normalizace.md)
7. [Tabulky s demo daty](docs/07_tabulky_s_demo_daty.md)
8. [Ukázka databáze v praxi](docs/08_ukazka_databaze_v_praxi.md)
9. [Generační skripty SQL v praxi](docs/09_generacni_skripty.md)
10. [Implementace](docs/10_implementace.md)
11. [Závěrečné shrnutí](docs/11_zaverecne_shrnuti.md)

## Základ projektu

Práce vychází z návrhu databázového schématu pro projekt Archaeo-LLM. Databáze je zaměřena na evidenci archeologických nálezů, zejména předmětů, kategorií, lokalit, fotografií, materiálů a rozšiřujících detailních údajů.

V rámci práce je použita struktura sedmi tabulek:

- category
- site
- artifact
- photo
- material
- artifact_material
- artifact_detail

## Webová prezentace

Součástí projektu je také jednoduchá webová dokumentace publikovaná prostřednictvím GitHub Pages:

https://19alex27.github.io/archaeo-llm-school-project/

Webová část slouží jako praktická ukázka toho, jak lze databázový projekt představit uživateli formou přehledné školní prezentace.
