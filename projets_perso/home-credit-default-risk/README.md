**Prédiction du défaut de remboursement (Home Credit)**  
**Objectif**  
Prédire si un client va rembourser son crédit, à partir de son historique :  
   
 sa demande actuelle, ses crédits passés (autres organismes et Home Credit) et ses remboursements.  
   
 Le modèle sert à repérer les clients à risque ; la mesure utilisée est l'**AUC**.  
**Données**  
Compétition Kaggle « Home Credit Default Risk » :  
   
 [https://www.kaggle.com/c/home-credit-default-risk](https://www.kaggle.com/c/home-credit-default-risk "https://www.kaggle.com/c/home-credit-default-risk")  
Les données **ne sont pas incluses** dans ce dépôt (taille importante et règles de la compétition).  
   
 Fichiers utilisés par le notebook :  
   
 `application_train.csv`, `bureau.csv`, `bureau_balance.csv`,  
   
 `previous_application.csv`, `installments_payments.csv`.  
Le notebook travaille sur un **échantillon aléatoire de 100 000 clients** (`random_state=42`).  
**Méthode**  
1. **Agrégation avec SQL (DuckDB)** : les tables secondaires (crédits d'autres organismes,  
   
 demandes précédentes, remboursements) sont résumées **par client**, puis jointes à la table principale.  
2. **Prétraitement** : valeurs manquantes (médiane), mise à l'échelle, encodage des variables catégorielles.  
3. **Modèles** : régression logistique (référence), puis LightGBM  
   
 (pondération de la classe rare avec `scale_pos_weight`).  
4. **Validation** : validation croisée stratifiée à 5 plis.  
5. **Interprétation** : importance des variables et valeurs SHAP.  
**Résultats (AUC)**  
  
|  |  |  
| - | - |  
| **Modèle** |  **AUC** |  
| Régression logistique |  0,751 (jeu de validation) |  
| LightGBM |  0,762 (moyenne de la validation croisée à 5 plis) |  
| LightGBM |  0,772(jeu de validation) |  
  
   
**Limites**  
- Échantillon de 100 000 clients, et non le jeu complet.  
- Les tables `POS_CASH_balance` et `credit_card_balance` ne sont pas utilisées.  
- Pas de réglage fin des hyperparamètres de LightGBM.  
- Résultats mesurés sur un jeu de validation, pas sur le jeu de test de la compétition.  
**Reproduire**  
1. Télécharger les données sur Kaggle et placer les cinq fichiers CSV **dans le même dossier que le notebook**.  
2. `pip install -r requirements.txt`  
3. Ouvrir et exécuter `home_credit.ipynb` (une base `hc.duckdb` est créée localement).  
