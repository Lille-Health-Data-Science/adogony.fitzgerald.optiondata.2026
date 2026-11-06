# Réponses aux questions · TP FHIR & OMOP
**Auteur :** Fitzgerald ADOGONY — M2 Data Science en Santé, Université de Lille  
**Date :** Novembre 2026  
**Projet :** Application Diabète — De l'export FHIR au mini-entrepôt OMOP  

---

## 1. Harmonisation et qualité des données (C1 & C2)

* **Pourquoi une moyenne brute n'a pas de sens ?**  
  Les données sources combinent trois unités de mesure différentes pour la glycémie : `mg/dL` (~100), `g/L` (~1,0) et `mmol/L` (~5,5), ainsi qu'un autre examen (`HbA1c` en `%`) et des statuts non exploitables (`cancelled`, `entered-in-error`). Calculer une moyenne brute reviendrait à sommer des grandeurs incomparables, faussant radicalement l'évaluation clinique (ex. une glycémie normale à 1,0 g/L apparaîtrait comme une hypoglycémie fatale si lue comme 1 mg/dL).
* **Impact de la standardisation OMOP :**  
  Après filtrage des statuts `final` et conversion vers l'unité de référence `mg/dL` (facteurs de conversion : $\times 100$ pour `g/L` et $\times 18{,}016$ pour `mmol/L`), 835 glycémies sont harmonisées. Conserver les colonnes `*_source_value` garantit la traçabilité et l'auditabilité clinique de l'entrepôt.

---

## 2. Analyse clinique et détection du diabète (C5)

* **Résultats du critère biologique ($\ge 2$ glycémies à jeun $\ge 126$ mg/dL) :**  
  * **59 patients** répondent au critère biologique de l'ADA/OMS.
  * Parmi eux, **9 patients n'ont aucun diagnostic de diabète codé** dans leur dossier (`Condition`).
* **Interprétation de la carte des patients (C5 bis) :**
  * 🟠 **Points orange dans la zone du critère (9 patients) :** Cas très probables de **sous-diagnostic** ou de **sous-codage**. Ces patients présentent une hyperglycémie biologique avérée mais sans diagnostic formellement documenté.
  * 🔵 **Points bleus hors de la zone (5 patients) :** Patients reconnus diabétiques mais avec moins de deux mesures $\ge 126$ mg/dL. Cela reflète soit un diabète bien équilibré sous traitement, soit un faible nombre de dosages disponibles dans l'historique.
* **Conséquence sans conversion :**  
  Le critère $\ge 126$ aurait silencieusement ignoré toutes les glycémies exprimées en `g/L` et en `mmol/L` (qui ne dépassent jamais 126 dans leur unité d'origine), manquant **41,2 % des glycémies pathologiques**.

---

## 3. Exploration et décision clinique (D1 & D2)

* **Sensibilité du seuil (D1) :**  
  En abaissant le seuil à $110$ mg/dL, **17 patients sans diagnostic** entrent dans le critère (au lieu de 9).  
  *Le choix du seuil est une décision strictement clinique et non technique* : il arbitre entre la **sensibilité** (dépistage précoce des états prédiabétiques) et la **spécificité** (éviter les faux positifs, l'anxiété patient et des examens inutiles). 126 mg/dL est le consensus international pour le diagnostic du diabète avéré.
* **Patient à la loupe (D2 - ex. `p-0100`) :**  
  Le patient `p-0100` présente **8 glycémies consécutives $\ge 126$ mg/dL** sans aucun diagnostic codé.  
  *Message au médecin traitant :* Alerte sur une anomalie glycémique chronique et répétée justifiant la pose formelle du diagnostic de diabète de type 2, la réalisation d'une HbA1c de référence et l'instauration d'un suivi thérapeutique adapté.

---

## 4. Défis complémentaires (D3)

1. **Part des glycémies échappées (Défi 1) :** Sur 289 glycémies $\ge 126$ mg/dL, **119 mesures (41,2 %)** auraient échappé au seuil sans conversion (100 % des mesures en `g/L` et `mmol/L`).
2. **Usage de l'HbA1c (Défi 2) :** Les 55 patients ayant bénéficié d'un dosage d'HbA1c (LOINC `4548-4`) ont **tous (100 %)** un diagnostic de diabète déjà codé. Cela illustre parfaitement la pratique clinique en France : l'HbA1c n'est pas utilisée en dépistage primaire, mais exclusivement dans le **suivi thérapeutique** des diabétiques connus.
3. **Glycémie maximale (Défi 3) :** La valeur la plus élevée est de **254,0 mg/dL** chez le patient **`p-0071`** (mesure brute de $2{,}54$ g/L).
