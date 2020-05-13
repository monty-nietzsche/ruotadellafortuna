<img width=12.5% src="https://github.com/anfederico/Clairvoyant/blob/master/media/Logo.png">
<img width=60% src="https://github.com/anfederico/Clairvoyant/blob/master/media/Clairvoyant.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
![Python](https://img.shields.io/badge/python-v3.6+-blue.svg)
[![Build Status](https://travis-ci.org/anfederico/Clairvoyant.svg?branch=master)](https://travis-ci.org/anfederico/Clairvoyant)
![Dependencies](https://img.shields.io/badge/dependencies-up%20to%20date-brightgreen.svg)
[![GitHub Issues](https://img.shields.io/github/issues/anfederico/Clairvoyant.svg)](https://github.com/anfederico/Clairvoyant/issues)
![Contributions welcome](https://img.shields.io/badge/contributions-welcome-orange.svg)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://opensource.org/licenses/MIT)

## Basic Overview

Inizialmente, non avevamo completato in tempo la fase di “build automation”, a causa principalmente dell’inesperienza 
nell’utilizzo di Maven. Quindi, benchè questa fase non fosse fondamentale, in fase di consegna avevamo specificato che l’esecuzione
del programma dovesse avvenire attraverso Eclipse. Successivamente, essendo stata data la possibilità di completare questa parte in
modo tale da poter avviare il programma a linea di comando, ci siamo rimessi sui nostri passi, ed abbiamo sviluppato la build 
automation con Ant, riuscendo a far partire il programma tramite questo strumento. Abbiamo utilizzato Ant perché, a nostro avviso,
ha una gestione complessiva più semplice, anche in fase di portabilità, che ci ha permesso, appunto, di raggiungere l’obiettivo.

## Apache Ant
Apache Ant è un software per l'automazione del processo di build. È simile a make, ma scritto in Java ed è principalmente orientato
allo sviluppo in Java. Ant è un progetto Apache, open source, ed è distribuito sotto licenza Apache. Esso si basa su script in
formato xml. Ogni build file definisce un project composto da target in cui sono elencati i task, le istruzioni da eseguire.
Nel progetto possono essere definite delle properties, coppie nome e valore immodificabili nel resto del progetto. I target 
possono avere delle dipendenze da altri target. La sintassi ant lascia libero l'autore del codice di usare una convenzione 
qualsiasi, back/forward slash per le directory, punto e virgola o due punti (; o :) per i separatori dei path (classpath). 
Ant converte tutto nella forma più appropriata alla piattaforma corrente. I comandi da noi utilizzati per avviare WOF, presenti 
nel nostro `build.xml`. 

## ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) Database WOF

There should be a Postgres database named `WOF` running on the localhost at port `5432`. 
The code for creating the table needed for the database is the following:
``` sql

CREATE TABLE public.matches (
    idmatch character varying(36) NOT NULL,
    creationtime character varying(10) NOT NULL,
    creationdate character varying(10) NOT NULL
);

CREATE TABLE public.matchwinners (
    idmatch character varying(36) NOT NULL,
    player character varying(36) NOT NULL,
    score numeric(6,0) NOT NULL
);

CREATE TABLE public.moves (
    idmove character varying(36) NOT NULL,
    player character varying(36) NOT NULL,
    movetype character varying(10) NOT NULL,
    points numeric(6,0) NOT NULL,
    idmatch character varying(36) NOT NULL,
    roundnumber numeric(1,0) NOT NULL
);

CREATE TABLE public.phrases (
    idphrase integer NOT NULL,
    phrase character varying(60) NOT NULL,
    theme character varying(60) NOT NULL
);

CREATE SEQUENCE public.phrase_idphrase_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;

ALTER SEQUENCE public.phrase_idphrase_seq OWNED BY public.phrases.idphrase;

CREATE TABLE public.roundparticipants (
    idmatch character varying(36) NOT NULL,
    roundnumber numeric(1,0) NOT NULL,
    player character varying(36) NOT NULL,
    isobserver boolean NOT NULL
);

CREATE TABLE public.rounds (
    roundnumber numeric(1,0) NOT NULL,
    phrase integer NOT NULL,
    idmatch character varying(36) NOT NULL
);

CREATE TABLE public.roundwinners (
    idmatch character varying(36) NOT NULL,
    roundnumber numeric(1,0) NOT NULL,
    player character varying(36) NOT NULL,
    score numeric(6,0) NOT NULL
);

CREATE TABLE public.users (
    iduser character varying(36) NOT NULL,
    isadmin numeric(1,0) NOT NULL,
    name character varying(50) NOT NULL,
    surname character varying(50) NOT NULL,
    nickname character varying(50) NOT NULL,
    email character varying(50) NOT NULL,
    password character varying NOT NULL
);

ALTER TABLE ONLY public.phrases ALTER COLUMN idphrase SET DEFAULT nextval('public.phrase_idphrase_seq'::regclass);
```

## ![#1589F0](https://via.placeholder.com/15/1589F0/000000?text=+) Avvio del modulo per il Server
```console
utente@computer:~$ cd rdfProject
utente@computer:rdfProject$ ant server
```

### serverRDF

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen1.png" alt="serverRdF" width="400"/>


The database credentials:

- username : `postgres`
- password : `postgres`
- hostname : `localhost`
- port     : `5432`

### serverGUI
<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen2.png" alt="serverRdF" width="400"/>

The email credentials:
- email : `la_ruota_della_fortuna@outlook.it`
- password : `No_reply`

### AdminSignIN

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen3.png" alt="serverRdF" width="400"/>

The administrator credentials:
- email: `admin@ruota.com`
- password : `password`

### Server Started! 

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen4.png" alt="serverRdF" width="400"/>


## ![#c5f015](https://via.placeholder.com/15/c5f015/000000?text=+)  Avvio Modulo per Utente

```console
utente@computer:~$ cd rdfProject
utente@computer:rdfProject$ javac playerRdF.java
utente@computer:rdfProject$ java playerRdF
```

### HostGUI
<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen5.png" alt="serverRdF" width="400"/>

### Utente SignIN

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen6.png" alt="serverRdF" width="400"/>

### RDFPlatform: Modulo Utente Giocatore

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen7.png" alt="serverRdF" width="600"/>

### Modulo schermata di gioco
<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen8.png" alt="serverRdF" width="600"/>

## Last Stable Release
```python
pip install clairvoyant
```

## Latest Development Changes
```bash
git clone https://github.com/anfederico/Clairvoyant
```

## Backtesting Signal Accuracy
During the testing period, the model signals to buy or sell based on its prediction for price
movement the following day. By putting your trading algorithm aside and testing for signal accuracy
alone, you can rapidly build and test more reliable models.

```python
from clairvoyant.engine import Backtest
import pandas as pd

features  = ["EMA", "SSO"]   # Financial indicators of choice
trainStart = 0               # Start of training period
trainEnd   = 700             # End of training period
testStart  = 701             # Start of testing period
testEnd    = 1000            # End of testing period
buyThreshold  = 0.65         # Confidence threshold for predicting buy (default = 0.65) 
sellThreshold = 0.65         # Confidence threshold for predicting sell (default = 0.65)
continuedTraining = False    # Continue training during testing period? (default = false)

# Initialize backtester
backtest = Backtest(features, trainStart, trainEnd, testStart, testEnd, buyThreshold, sellThreshold, continuedTraining)

# A little bit of pre-processing
data = pd.read_csv("SBUX.csv", date_parser=['date'])
data = data.round(3)                                    

# Start backtesting and optionally modify SVC parameters
# Available paramaters can be found at: http://scikit-learn.org/stable/modules/generated/sklearn.svm.SVC.html
backtest.start(data, kernel='rbf', C=1, gamma=10)
backtest.conditions()
backtest.statistics()  
backtest.visualize('SBUX')
```

#### Output
```text
------------ Data Features ------------

X1: EMA
X2: SSO

---------------------------------------

----------- Model Arguments -----------

kernel: rbf
C: 1
gamma: 10

---------------------------------------

---------  Engine Conditions ----------

Training: 2013-03-01 -- 2015-12-09
Testing:  2015-12-10 -- 2017-02-17
Buy Threshold: 65.0%
Sell Threshold: 65.0%
Continued Training: False

---------------------------------------

------------- Statistics --------------

Total Buys: 170
Buy Accuracy: 68.24%
Total Sells: 54
Sell Accuracy: 59.3%

---------------------------------------
```

<img src="https://github.com/anfederico/Clairvoyant/blob/master/media/SBUX.png">

## Simulate a Trading Strategy
Once you've established your model can accurately predict price movement a day in advance, 
simulate a portfolio and test your performance with a particular stock. User defined trading logic
lets you control the flow of your capital based on the model's confidence in its prediction
and the following next day outcome.

```python
def logic(account, today, prediction, confidence):
    
    if prediction == 1:
        Risk         = 0.30
        EntryPrice   = today['close']
        EntryCapital = account.BuyingPower*Risk
        if EntryCapital >= 0:
            account.EnterPosition('Long', EntryCapital, EntryPrice)

    if prediction == -1:
        ExitPrice = today['close']
        for Position in account.Positions:  
            if Position.Type == 'Long':
                account.ClosePosition(Position, 1.0, ExitPrice)


simulation = backtester.Simulation(features, trainStart, trainEnd, testStart, testEnd, buyThreshold, sellThreshold, continuedTraining)
simulation.start(data, 1000, logic, kernel='rbf', C=1, gamma=10)
simulation.statistics()
simulation.chart('SBUX')
```

#### Output

```text
------------- Statistics --------------

Buy and Hold : -6.18%
Net Profit   : -61.84
Strategy     : 5.82%
Net Profit   : 58.21
Longs        : 182
Sells        : 168
Shorts       : 0
Covers       : 0
--------------------
Total Trades : 350

---------------------------------------
```

<img src="https://raw.githubusercontent.com/anfederico/Clairvoyant/master/media/Chart.png">

## Other Projects
#### Intensive Backtesting
The primary purpose of this project is to rapidly test datasets on machine learning algorithms (specifically Support Vector Machines). While the Simulation class allows for basic strategy testing, there are other projects more suited for that task. Once you've tested patterns within your data and simulated a basic strategy, I'd recommend taking your model to the next level with:
```text
https://github.com/anfederico/Gemini
```

#### Social Sentiment Scores
The examples shown use data derived from a project where we are data mining social media and performing stock sentiment analysis. To get an idea of how we do that, please take a look at:
```text
https://github.com/anfederico/Stocktalk
```

## Notes
#### Multivariate Functionality
Remember, more is not always better!
```python
variables = ["SSO"]                            # 1 feature
variables = ["SSO", "SSC"]                     # 2 features
variables = ["SSO", "SSC", "RSI"]              # 3 features
variables = ["SSO", "SSC", "RSI", ... , "Xn"]  # n features
```

## Contributing
Please take a look at our [contributing](https://github.com/anfederico/Clairvoyant/blob/master/CONTRIBUTING.md) guidelines if you're interested in helping!
#### Pending Features
- Export model
- Support for multiple sklearn SVM models
- Visualization for models with more than 2 features
