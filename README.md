# WEB SCRAPPING - OSINT-Maritime-Marmara

**Detection de transbordements suspects en Mer de Marmara — Pipeline OSINT et analyse relationnelle**

Ce projet met en place un pipeline automatise de collecte, d'enrichissement et d'analyse de donnees AIS (Automatic Identification System) pour detecter des interactions navire-a-navire (ship-to-ship transfers) potentiellement suspectes en Mer de Marmara. L'ensemble repose exclusivement sur des sources OSINT gratuites.

*Projet developpe dans le cadre d'un processus de recrutement pour une mission OSINT au sein du CENTOPS MED (Centre des Operations Mediterranee) de la Marine nationale.*

---

## Objectif

Identifier des co-presences portuaires entre navires (notamment tankers) dans les zones strategiques de la Mer de Marmara, et attribuer un score de suspicion base sur des criteres temporels, typologiques et geographiques.

---

## Pipeline en 3 etapes

| Etape | Fonction | Entree | Sortie |
|-------|----------|--------|--------|
| **Step 1 — Collecte** | Scraping des navires presents dans chaque port | Liste de ports par zone | Sightings par port (JSON) |
| **Step 2 — Enrichissement** | Recuperation des metadonnees (IMO, type, pavillon, escales) | Sightings Step 1 | Navires enrichis avec metadata (JSON) |
| **Step 3 — Analyse relationnelle** | Detection de co-presences et scoring des paires | Metadata Step 2 | Paires scorees et classees (JSON / Excel) |

---

## Zones couvertes

La Mer de Marmara est decoupee en 5 zones operationnelles. Trois sont implementees dans les notebooks :

| Zone | Contexte | Notebook |
|------|----------|----------|
| **Golfe d'Izmit** | Zone industrielle, raffineries, trafic tanker dense | `1__Scrapping_Zone_Izmit` |
| **Dardanelles** | Detroits, zone de transit strategique | `2__Scrapping_Zone_Dardanelles` |
| **Rive Sud** | Mouillages, zones secondaires | `3__Scrapping_Zone_Rive_Sud` |

---

## Systeme de scoring

Le score de suspicion combine deux niveaux :

- **Score MACRO** : historique relationnel global de la paire de navires (nombre d'interactions passees)
- **Score MICRO** : score par evenement de co-presence, pondere par le type de navire, le type de zone portuaire et le score macro

Formule : `Score_MICRO = Score_base(overlap) x mult_type x mult_zone x mult_macro`

Chaque score est concu pour etre explicable et defendable.

---

## Source de donnees

| Attribut | Detail |
|----------|--------|
| **Source** | VesselFinder (vesselfinder.com) |
| **Acces** | Gratuit, sans authentification |
| **Methode** | Scraping HTML des pages ports via Playwright |
| **Donnees** | MMSI, IMO, type de navire, pavillon, position, destination, escales |

Trois sources ont ete evaluees (MarineTraffic, VesselFinder, AISHub). Seul VesselFinder satisfait les 4 criteres : acces HTTP, pas d'authentification, champs requis presents, scraping possible.

---

## Contraintes du projet

| Contrainte | Implication |
|------------|-------------|
| Budget zero | Aucune API payante, OSINT gratuit uniquement |
| Reproductibilite | Pipeline entierement automatise, executable sur toute zone |
| Justifiabilite | Chaque score explicable et defendable |

---

## Structure du projet

```
OSINT-Maritime-Marmara/
├── 0__Methodologie_Cartographie.ipynb      # Documentation methodologique complete
├── 1__Scrapping_Zone_Izmit.ipynb            # Pipeline collecte + analyse — Golfe d'Izmit
├── 2__Scrapping_Zone_Dardanelles.ipynb      # Pipeline collecte + analyse — Dardanelles
├── 3__Scrapping_Zone_Rive_Sud.ipynb         # Pipeline collecte + analyse — Rive Sud
├── data/                                    # Fichiers JSON intermediaires et resultats
└── README.md
```

---

## Installation et execution

```bash
git clone https://github.com/Midou94f/OSINT-Maritime-Marmara
cd OSINT-Maritime-Marmara
pip install playwright pandas openpyxl nest_asyncio
playwright install chromium
```

**Prerequis :** Python >= 3.10. Le scraping necessite Playwright en mode visible (le mode headless est bloque par VesselFinder).

Execution zone par zone :

```bash
jupyter notebook 1__Scrapping_Zone_Izmit.ipynb
```

---

## Auteur

Projet realise par **Mehdi Fehri** — Master 2 Statistique, Econometrie et Data Science, Universite de Strasbourg.

---

## Licence

Voir le fichier [LICENSE](LICENSE) pour les details.

---

> *Ce projet est une demonstration de competences OSINT. Les donnees collectees sont publiquement accessibles et utilisees a des fins d'analyse uniquement.*
