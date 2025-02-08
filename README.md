# BioWatch - Vital Signs Monitoring System
## Project description
BioWatch is a system for monitoring patients' vital signs, designed to support healthcare personnel in managing patients remotely by collecting, processing, and displaying real-time vital data.

## System Architecture
<img src="book/images/data pipeline(2).png" alt="Pipeline" width="1500">
The system architecture is based on:

1. **VitalDB**: External database of 6,388 patients consisting of intraoperative biosignals and clinical information, which is used in the project to simulate a real-time data source.
2. **VitalDBscraper** : For each patient, a dedicated Python module (e.g., vitaldbscraper_patient_1, vitaldbscraper_patient_2, etc.) extracts real patient data from the database.
3. **Fluentd** : A powerful open-source data collection tool that receives patient data extracted from Vital DB and transfers it to Apache Kafka.
4. **Apache Kafka** : Distributed messaging system for real-time data transmission, ensuring scalability and reliability.
5. **Apache Spark** : Framework for distributed processing and real-time analysis of data transmitted from Kafka.
6. **Elasticsearch** : Processed data is indexed and stored for fast and efficient retrieval.
7. **Kibana** : Intuitive dashboards for real-time monitoring of vital parameters, based on data stored in Elasticsearch.

## Workflow
1. **Data Collection:** Retrieving clinical data and vital parameters via **VitalDB Web API** and **VitalDB Python library**.
2. **Preprocessing:** Only data from the **Solar8000M** device, which captures the essential parameters for human life, is extracted. The parameters extracted are: Heart Rate, Respiratory Rate, Body Temperature, Oxygen Saturation, Systolic Blood Pressure and Diastolic Blood Pressure.  **50%** of the least significant data is removed to account for the device's initial and final phases, during which the readings are either still **stabilizing** or **experiencing decay**, making them unreliable.
3. **Streaming:** Sending data to Fluentd via **HTTP** and forwarding it to Kafka. In order to create a realistic simulation of a real-time data source, the data is sent to Fluentd **every 2 seconds**, according to the device's sampling frequency.
4. **Data Enrichment:** Calculation of derived parameters (Pulse Pressure, BMI, MAP) with **Apache Spark** and risk classification using a **Random Forest** machine learning model trained on **Human Vital Signs Dataset (Kaggle)**.
6. **Indexing and Visualization:** Data is indexed to enable **fast and efficient searching**, while a dashboard visualization displays the evolution of parameters over time, helping to identify **trends** and detect **anomalies**.

## Steps to Run the Project

### Requirements
- Install **Docker** and **Docker Compose**
- Create an account on **Elastic Cloud**

### Installation and Setup
```sh
# Clone the repository
git clone url_github_progettotap
cd progettotap

# Configure the .env file
nano .env
# Set the following parameters:
# NUM_PATIENTS, PATIENTS_IDS, ELASTIC_HOST, ELASTIC_USER, ELASTIC_PASSWORD

# Assign permissions
chmod -R 777 *

# Build the Spark container
docker build spark --tag tap:spark

# Train the model
docker run --hostname spark -p 4040:4040 -it --rm \
  -v ./spark/code/:/opt/tap/ \
  -v ./spark/dataset:/tmp/dataset tap:spark \
  /opt/spark/bin/spark-submit /opt/tap/risk_prediction_model.py

# Generate the docker-compose file
bash generate-docker-compose.sh

# Start the containers
docker compose up
```

### Elastic Cloud Configuration
1. Access **Elastic Cloud Personal Deployment**.
2. Import the dashboard and index the data (index `vitalparameters`).

## Data Visualization and Analysis
- **Time-series charts** for each vital parameter.
- **Color-coded gauges** for real-time risk assessment:
  - 🟢 **Green:** Normal values.
  - 🟡 **Yellow:** Borderline values, attention required.
  - 🔴 **Red:** Critical values, potential emergency.

## Conclusion
This system automates real-time risk assessment, overcoming fixed threshold limitations and enhancing medical decision support through **Machine Learning and Big Data Analytics**.



   
   

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


 
