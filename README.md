# M2 Data Science en Santé · Projet Diabète (FHIR & OMOP)

**Étudiant :** Fitzgerald ADOGONY  
**Formation :** M2 Data Science en Santé — Université de Lille  
**Enseignant :** Antoine Teston  
**Échéance :** 7 novembre 2026  

---

## 📌 Présentation du projet

Ce projet porte sur l'interopérabilité des données de santé et la modélisation sous le standard **OMOP CDM** à partir d'un export **FHIR R4** :
1. **Échange et requêtage FHIR :** Modélisation de ressources d'observation (`Observation`), requêtage de serveur FHIR via API REST (`hapi.fhir.org`), pagination et interrogation avec FHIRPath.
2. **Harmonisation et pipeline ETL vers OMOP (DuckDB) :**
   * Filtrage des données exploitables (statut `final`, exclusion de l'HbA1c du flux glycémique brut).
   * Harmonisation des unités hétérogènes (`g/L`, `mmol/L` $\rightarrow$ `mg/dL`).
   * Construction des tables OMOP `PERSON` et `MEASUREMENT` avec leurs identifiants de concepts standardisés.
   * Analyse clinique du dépistage du diabète de type 2 (critère $\ge 2$ glycémies $\ge 126$ mg/dL) et confrontation aux diagnostics codés (`Condition`).
   * Export des tables au format Parquet (`sortie/`).
3. **Exploration & Défis cliniques :**
   * Étude de la sensibilité du seuil diagnostique (110 vs 126 mg/dL).
   * Analyse de l'usage réel de l'HbA1c (100 % en suivi thérapeutique).
   * Visualisation chronologique des glycémies par patient.

---

## 📂 Contenu du dépôt & Livrables

* 📓 **[`seance1_fhir/seance1_etudiant.ipynb`](seance1_fhir/seance1_etudiant.ipynb)** : Notebook Jupyter entièrement complété (parties A, B, C et D).
* 📄 **[`REPONSES.md`](REPONSES.md)** : Synthèse rédigée d'une demi-page répondant aux questions cliniques, méthodologiques et aux défis du TP.
* 📈 **[`courbe_patient.png`](courbe_patient.png)** : Graphique de suivi glycémique du patient `p-0100` (8 glycémies $\ge 126$ mg/dL sans diagnostic codé).
* 📁 `seance1_fhir/donnees/` : Jeu de données synthétiques FHIR (`Patient.ndjson`, `Observation.ndjson`, `Condition.ndjson`) et table de correspondance `concepts.csv`.
