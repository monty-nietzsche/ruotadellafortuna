<h1 align="center"> RUOTA DELLA FORTUNA </h1>


# Panoramica

Inizialmente, non avevamo completato in tempo la fase di “build automation”, a causa
principalmente dell’inesperienza nell’utilizzo di Maven. Quindi, benchè questa fase non fosse
fondamentale, in fase di consegna avevamo specificato che l’esecuzione del programma dovesse
avvenire attraverso Eclipse. Successivamente, essendo stata data la possibilità di completare questa
parte in modo tale da poter avviare il programma a linea di comando, ci siamo rimessi sui nostri
passi, ed abbiamo sviluppato la build automation con Ant, riuscendo a far partire il programma
tramite questo strumento. Abbiamo utilizzato Ant perché, a nostro avviso, ha una gestione
complessiva più semplice, anche in fase di portabilità, che ci ha permesso, appunto, di raggiungere
l’obiettivo.

# ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)  Apache Ant
Apache Ant è un software per l’automazione del processo di build. È simile a make, ma scritto in Java
ed è principalmente orientato allo sviluppo in Java. Ant è un progetto Apache, open source, ed è
distribuito sotto licenza Apache. Esso si basa su script in formato xml. Ogni build file definisce un
project composto da target in cui sono elencati i task, le istruzioni da eseguire. Nel progetto possono
essere definite delle properties, coppie nome e valore immodificabili nel resto del progetto. I target
possono avere delle dipendenze da altri target. La sintassi ant lascia libero l’autore del codice di usare
una convenzione qualsiasi, back/forward slash per le directory, punto e virgola o due punti (; o :)
per i separatori dei path (classpath). Ant converte tutto nella forma più appropriata alla piattaforma
corrente. I comandi da noi utilizzati per avviare WOF, presenti nel nostro `build.xml`. 

# ![#1589F0](https://via.placeholder.com/15/1589F0/000000?text=+) Avvio del modulo per il Server

```console
utente@computer:~$ cd RDFServer
utente@computer: RDFServer$ ant build
utente@computer: RDFServer$ ant ServerRdf
```

## Connessione al database 

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen1.png" alt="Create databaseManager" width="400"/>

```text
---------------- database credentials ------------------
username        : postgres
password        : postgres
hostname        : localhost
port            : 5432
---------------------------------------------------------
```

## Connessione all'indirizzo e-mail del gioco

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen2.png" alt="Create emailManager" width="400"/>

```text
---------------- email credentials ------------------
email           : la_ruota_della_fortuna@outlook.it
password        : postgres
-----------------------------------------------------
```

## Accesso amministratore

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen3.png" alt="Admin SignIn" width="400"/>

```text
------------ administrator credentials --------------
email           : admin@ruota.it
password        : ruota
-----------------------------------------------------
```

## Server avviato!

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen4.png" alt="Server Running..." width="400"/>


# ![#c5f015](https://via.placeholder.com/15/c5f015/000000?text=+)  Avvio Modulo per Utente

```console
utente@computer:~$ cd RDFClient
utente@computer: RDFClient$ ant build
utente@computer: RDFClient $ ant PlayerRdf
```

## Connessione all'host remoto

L'utente annota l'indirizzo del computer remoto. Nel nostro caso, lo è `localhost`.

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen5.png" alt="Connect to Host" width="400"/>

## Accesso giocatore

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen6.png" alt="SignIn Player" width="400"/>

```text
------------- Player Tizio credentials --------------
email           : tizio@ruota.it
password        : ruota
-----------------------------------------------------
```

## Piattaforma di gioco

Una volta effettuato l'accesso, viene visualizzata la piattaforma RdFPlatform.

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen7.png" alt="Game Platform" width="600"/>

## Schermata di gioco

Se il giocatore fa clic su `Crea Partita`, viene visualizzata la schermata di gioco.

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen8.png" alt="MatchInterface" width="600"/>

## Iniziare una partita

Ora Tizio ha creato una partita. Usa le seguenti credenziali per accedere a due giocatori aggiuntivi. Nella schermata della piattaforma RDF, fai clic su `+Giocatore` per unirsi alla partita creata da Tizio.

```text
-------------- Player Caio credentials --------------
email           : caio@ruota.it
password        : ruota
-----------------------------------------------------
```


```text
----------- Player Sempronio credentials ------------
email           : sempronio@ruota.it
password        : ruota
-----------------------------------------------------
```

Una volta che i due giocatori `Caio` e `Sempronio` sono iscritti alla partita creata da Tizio, l'interfaccia partita diventa attivo ed i tre giocatori saranno in grado di giocare.

<img src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/screen9.png" alt="MatchOnGoing" width="600"/>

# ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) Contributions
L'attuale progetto è stato realizzato dal seguente team:
- Project Manager : Mohamed Ghachem
- Team            : Guilio Delia; Lorris Bruno and Gabriele Sbrò
