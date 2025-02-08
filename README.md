# BioWatch - Vital Signs Monitoring System
## Descrizione del Progetto
Il progetto implementa una pipeline di acquisizione, elaborazione e analisi di dati clinici e biosensori intraoperatori. Utilizza tecnologie containerizzate per garantire scalabilità, portabilità e facilità di distribuzione.

Il dataset è composto da 6.388 pazienti, con dati raccolti a 500 Hz per i biosensori e a intervalli tra 1 e 7 secondi per le informazioni numeriche. Include oltre 60 parametri clinici relativi agli interventi chirurgici.

## Architettura del Sistema
L'architettura del sistema si basa su:
- **Docker & Docker Compose** per la containerizzazione e gestione dei servizi.
- **VitalDB Web API** per il recupero dei dati clinici.
- **Solar 8000M** per l'acquisizione dei parametri vitali.
- **Fluentd** per l'inoltro dei dati a **Kafka**.
- **Apache Spark** per l'arricchimento e analisi dei dati.
- **Machine Learning** per la classificazione del rischio del paziente.
- **Elasticsearch** per l'indicizzazione e visualizzazione dei dati.

## Flusso di Lavoro
1. **Raccolta Dati:** Recupero dei dati clinici e vitali tramite **VitalDB Web API** e **Solar 8000M**.
2. **Preprocessing:** Campionamento dei dati ogni 2 secondi e rimozione del 50% meno significativo (interquartile range).
3. **Streaming:** Invio dei dati a **Fluentd** tramite HTTP e inoltro a **Kafka**.
4. **Arricchimento Dati:** Calcolo di parametri derivati (Pulse Pressure, BMI, MAP) con **Apache Spark**.
5. **Analisi AI:** Classificazione del rischio tramite un modello addestrato su **Human Vital Signs Dataset (Kaggle)**.
6. **Indicizzazione e Visualizzazione:** Elasticsearch per il recupero veloce dei dati e dashboard con grafici.

## Passaggi per Eseguire il Progetto

### Requisiti
- Installare **Docker** e **Docker Compose**
- Creare un account su **Elastic Cloud**

### Installazione e Setup
```sh
# Clonare il repository
git clone url_github_progettotap
cd progettotap

# Configurare il file .env
nano .env
# Impostare i seguenti parametri:
# NUM_PATIENTS, PATIENTS_IDS, ELASTIC_HOST, ELASTIC_USER, ELASTIC_PASSWORD

# Assegnare i permessi
chmod -R 777 *

# Costruire il container Spark
docker build spark --tag tap:spark

# Addestrare il modello
docker run --hostname spark -p 4040:4040 -it --rm \
  -v ./spark/code/:/opt/tap/ \
  -v ./spark/dataset:/tmp/dataset tap:spark \
  /opt/spark/bin/spark-submit /opt/tap/risk_prediction_model.py

# Generare il file docker-composeash generate-docker-compose.sh

# Avviare i container
docker compose up
```

### Configurazione di Elastic Cloud
1. Accedere a **Elastic Cloud Personal Deployment**.
2. Importare la dashboard e indicizzare i dati (index `vitalparameters`).

## Visualizzazione e Analisi Dati
- **Grafici temporali** per ogni parametro vitale.
- **Gauge colorati** per valutare il rischio in tempo reale:
  - 🟢 **Verde:** Valori nella norma.
  - 🟡 **Giallo:** Valori al limite, richiede attenzione.
  - 🔴 **Rosso:** Valori critici, possibile emergenza.

## Conclusione
Questo sistema automatizza la valutazione del rischio in tempo reale, superando i limiti delle soglie fisse e migliorando il supporto alle decisioni mediche tramite l'uso di **Machine Learning e Big Data Analytics**.


 
