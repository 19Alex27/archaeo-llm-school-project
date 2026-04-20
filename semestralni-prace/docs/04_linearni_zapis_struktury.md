# 4. Lineární zápis struktury

Lineární zápis představuje stručnou textovou podobu E-R modelu. Primární klíče jsou v zápisu podtrženy a cizí klíče jsou označeny symbolem #.

- CATEGORY (**<u>category_id</u>**, category_name)
- SITE (**<u>site_id</u>**, site_name, country, region, latitude, longitude, description)
- ARTIFACT (**<u>artifact_id</u>**, #category_id, #site_id, inventory_code, title, period_name, estimated_date, short_description)
- PHOTO (**<u>photo_id</u>**, #artifact_id, file_name, alt_text, is_primary)
- MATERIAL (**<u>material_id</u>**, material_name, description)
- ARTIFACT_MATERIAL (**<u>#artifact_id</u>**, **<u>#material_id</u>**, note)
- ARTIFACT_DETAIL (**<u>#artifact_id</u>**, weight_g, height_mm, width_mm, condition_state, discovery_year, detailed_description)

## Poznámka k zápisu

Hlavní tabulkou databáze je ARTIFACT. Tato tabulka reprezentuje konkrétní archeologický nález a pomocí cizích klíčů #category_id a #site_id odkazuje na kategorii nálezu a lokalitu.

TABULKA PHOTO je připojena k nálezu pomocí cizího klíče #artifact_id. Díky tomu může mít jeden archeologický nález více fotografií.

TABULKA ARTIFACT_MATERIAL řeší vztah M:N mezi archeologickými nálezy a materiály. Proto má složený primární klíč tvořený dvojicí #artifact_id a #material_id.

TABULKA ARTIFACT_DETAIL používá artifact_id zároveň jako primární i cizí klíč. Tím je vyjádřeno, že detailní údaje patří vždy ke konkrétnímu archeologickému nálezu.
