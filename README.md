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

# ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) Database WOF

There should be a Postgres database named `WOF` running on the localhost at port `5432`.

## Creating the database tables

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

## Adding data to the table `phrases`

```sql
INSERT INTO public.phrases VALUES (1, 'un fenomeno di costume', 'La moda dei bikini');
INSERT INTO public.phrases VALUES (2, 'il pane di ramerino', 'Specialità toscana');
INSERT INTO public.phrases VALUES (3, 'magnetofono', 'Un apparecchio');
INSERT INTO public.phrases VALUES (4, 'anche i chili sono a contratto', 'Al moulin rouge');
INSERT INTO public.phrases VALUES (5, 'l''uccellino più puntuale', 'Il cucu');
INSERT INTO public.phrases VALUES (6, 'il pianeta con piu lune', 'Saturno');
INSERT INTO public.phrases VALUES (7, 'Il diacono', 'è un futuro sacerdote');
INSERT INTO public.phrases VALUES (8, 'e il simbolo della croce', 'L''albero di natale');
INSERT INTO public.phrases VALUES (9, 'lo puoi trovare sul canale quarantesei', 'Cartoonito');
INSERT INTO public.phrases VALUES (10, 'vinsero la loro battaglia grazie alla loro foga', 'Le amazzoni');
INSERT INTO public.phrases VALUES (11, 'bast era una divinità con la testa da felino ', 'Nell''antico egitto');
INSERT INTO public.phrases VALUES (12, 'Francamente me ne infischio', 'Via col vento, 1939');
INSERT INTO public.phrases VALUES (13, 'Via col vento', 'Francamente me ne infischio');
INSERT INTO public.phrases VALUES (14, 'Il Padrino', 'Gli farò un offerta che non potrà rifiutare');
INSERT INTO public.phrases VALUES (15, 'Star Wars', 'Che la Forza sia con te');
INSERT INTO public.phrases VALUES (16, 'Taxi driver', 'Ma dici a me');
INSERT INTO public.phrases VALUES (17, 'Apocalypse Now', 'Mi piace l odore del napalm al mattino');
INSERT INTO public.phrases VALUES (18, 'Il mistero del falco', 'La materia di cui sono fatti i sogni');
INSERT INTO public.phrases VALUES (19, 'Lucio Dalla', 'dice ciao al 900');
INSERT INTO public.phrases VALUES (20, 'personaggio biblico', 'Giacobbe');
INSERT INTO public.phrases VALUES (21, 'Stivali geografici', 'italia arabia nuova zelanda');
INSERT INTO public.phrases VALUES (22, 'nel medioevo', 'niente tv solo giullari e trovatori ');
INSERT INTO public.phrases VALUES (23, 'tom e jerry', 'eroi di hanna e barbera');
```

## Adding users

```sql
INSERT INTO public.users VALUES ('ce24665b-be73-4c1d-b541-e3f9830422b7', 0, 'Amminestratore', 'del Gioco', 'admin', 'admin@ruota.it', 'fb8e395602781f95279f897297fa350c');
INSERT INTO public.users VALUES ('ce24665b-be73-4c1d-b541-e3f9830422b7', 0, 'Tizio', 'Baggio', 'tizzio', 'tizio@ruota.it', 'fb8e395602781f95279f897297fa350c');
INSERT INTO public.users VALUES ('ce24665b-be73-4c1d-b541-e3f9830422b7', 0, 'Caio', 'Baggio', 'caio', 'caio@ruota.it', 'fb8e395602781f95279f897297fa350c');
INSERT INTO public.users VALUES ('ce24665b-be73-4c1d-b541-e3f9830422b7', 0, 'Sempronio', 'Baggio', 'sempronio', 'sempronio@ruota.it', 'fb8e395602781f95279f897297fa350c');
```

## Some foreign key constraints

```sql
SELECT pg_catalog.setval('public.phrase_idphrase_seq', 1, false);

ALTER TABLE ONLY public.rounds
    ADD CONSTRAINT round_pkey PRIMARY KEY (idmatch, roundnumber);

ALTER TABLE ONLY public.roundwinners
    ADD CONSTRAINT roundwinner_pkey PRIMARY KEY (idmatch, roundnumber, player);

ALTER TABLE ONLY public.roundparticipants
    ADD CONSTRAINT roundparticipants_pkey PRIMARY KEY (idmatch, roundnumber, player);

ALTER TABLE ONLY public.matches
    ADD CONSTRAINT match_pkey PRIMARY KEY (idmatch);

ALTER TABLE ONLY public.matchwinners
    ADD CONSTRAINT matchwinner_pkey PRIMARY KEY (idmatch, player);

ALTER TABLE ONLY public.moves
    ADD CONSTRAINT move_pkey PRIMARY KEY (idmove);

ALTER TABLE ONLY public.phrases
    ADD CONSTRAINT phrase_phrase_key UNIQUE (phrase);

ALTER TABLE ONLY public.phrases
    ADD CONSTRAINT phrase_pkey PRIMARY KEY (idphrase);

ALTER TABLE ONLY public.users
    ADD CONSTRAINT users_email_key UNIQUE (email);

ALTER TABLE ONLY public.users
    ADD CONSTRAINT users_nickname_key UNIQUE (nickname);

ALTER TABLE ONLY public.users
    ADD CONSTRAINT users_pkey PRIMARY KEY (iduser);

ALTER TABLE ONLY public.rounds
    ADD CONSTRAINT round_idmatch_fkey FOREIGN KEY (idmatch) REFERENCES public.matches(idmatch);

ALTER TABLE ONLY public.rounds
    ADD CONSTRAINT round_phrase_fkey FOREIGN KEY (phrase) REFERENCES public.phrases(idphrase);

ALTER TABLE ONLY public.roundwinners
    ADD CONSTRAINT roundwinner_idmatch_fkey FOREIGN KEY (idmatch, roundnumber) REFERENCES public.rounds(idmatch, roundnumber);

ALTER TABLE ONLY public.roundwinners
    ADD CONSTRAINT roundwinner_player_fkey FOREIGN KEY (player) REFERENCES public.users(iduser);

ALTER TABLE ONLY public.roundparticipants
    ADD CONSTRAINT roundparticipant_round_fkey FOREIGN KEY (idmatch, roundnumber) REFERENCES public.rounds(idmatch, roundnumber);

ALTER TABLE ONLY public.roundparticipants
    ADD CONSTRAINT roundparticipant_player_fkey FOREIGN KEY (player) REFERENCES public.users(iduser);
    
ALTER TABLE ONLY public.matchwinners
    ADD CONSTRAINT matchwinner_idmatch_fkey FOREIGN KEY (idmatch) REFERENCES public.matches(idmatch);

ALTER TABLE ONLY public.matchwinners
    ADD CONSTRAINT matchwinner_player_fkey FOREIGN KEY (player) REFERENCES public.users(iduser);

ALTER TABLE ONLY public.moves
    ADD CONSTRAINT move_idmatch_fkey FOREIGN KEY (idmatch, roundnumber) REFERENCES public.rounds(idmatch, roundnumber);

ALTER TABLE ONLY public.moves
    ADD CONSTRAINT move_player_fkey FOREIGN KEY (player) REFERENCES public.users(iduser);

```

# ![#1589F0](https://via.placeholder.com/15/1589F0/000000?text=+) Avvio del modulo per il Server
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
