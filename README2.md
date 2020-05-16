<img style="text-align:center" src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/logo.png" alt="Create databaseManager" width="400"/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

![Java](https://img.shields.io/badge/java-v8.0-blue.svg)
![Group work](https://img.shields.io/badge/group%20work-yes-brightgreen.svg)
![Works online](https://img.shields.io/badge/works%20online-no-red.svg)
![RMI](https://img.shields.io/badge/rmi%20technology-yes-brightgreen.svg)
![Contributions welcome](https://img.shields.io/badge/contributions-welcome-orange.svg)


## Panoramica

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

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<!-- WARNING: Eclipse auto-generated file.
              Any modifications will be overwritten.
              To include a user specific buildfile here, simply create one in the same
              directory with the processing instruction <?eclipse.ant.import?>
              as the first entry and export the buildfile again. --><project basedir="." default="build" name="RDF Client">
    <property environment="env"/>
    <property name="ECLIPSE_HOME" value="../../../../Desktop/Eclipse.app/Contents/Eclipse/"/>
    <property name="debuglevel" value="source,lines,vars"/>
    <property name="target" value="1.8"/>
    <property name="source" value="1.8"/>
    <path id="RDF Client.classpath">
        <pathelement location="bin"/>
        <pathelement location="jar/opencsv-4.0.jar"/>
        <pathelement location="serverExternal.jar"/>
    </path>
    <target name="init">
        <mkdir dir="bin"/>
        <copy includeemptydirs="false" todir="bin">
            <fileset dir="src">
                <exclude name="**/*.ucls"/>
                <exclude name="**/*.useq"/>
                <exclude name="**/*.java"/>
            </fileset>
        </copy>
        <copy includeemptydirs="false" todir="bin">
            <fileset dir=".">
                <exclude name="**/*.ucls"/>
                <exclude name="**/*.useq"/>
                <exclude name="**/*.java"/>
                <exclude name="src/"/>
                <exclude name="jar/"/>
                <exclude name="serverExternal/"/>
            </fileset>
        </copy>
    </target>
    <target name="clean">
        <delete dir="bin"/>
    </target>
    <target depends="clean" name="cleanall"/>
    <target depends="build-subprojects,build-project" name="build"/>
    <target name="build-subprojects"/>
    <target depends="init" name="build-project">
        <echo message="${ant.project.name}: ${ant.file}"/>
        <javac debug="true" debuglevel="${debuglevel}" destdir="bin" includeantruntime="false" source="${source}" target="${target}">
            <src path="src"/>
            <src path="."/>
            <exclude name="src/"/>
            <exclude name="jar/"/>
            <exclude name="serverExternal/"/>
            <classpath refid="RDF Client.classpath"/>
        </javac>
    </target>
    <target description="Build all projects which reference this project. Useful to propagate changes." name="build-refprojects"/>
    <target description="copy Eclipse compiler jars to ant lib directory" name="init-eclipse-compiler">
        <copy todir="${ant.library.dir}">
            <fileset dir="${ECLIPSE_HOME}/plugins" includes="org.eclipse.jdt.core_*.jar"/>
        </copy>
        <unzip dest="${ant.library.dir}">
            <patternset includes="jdtCompilerAdapter.jar"/>
            <fileset dir="${ECLIPSE_HOME}/plugins" includes="org.eclipse.jdt.core_*.jar"/>
        </unzip>
    </target>
    <target description="compile project with Eclipse compiler" name="build-eclipse-compiler">
        <property name="build.compiler" value="org.eclipse.jdt.core.JDTCompilerAdapter"/>
        <antcall target="build"/>
    </target>
    <target name="AdminRdf">
        <java classname="userRDF.AdminRdf" failonerror="true" fork="yes">
            <classpath refid="RDF Client.classpath"/>
        </java>
    </target>
    <target name="PlayerRdf">
        <java classname="userRDF.PlayerRdf" failonerror="true" fork="yes">
            <classpath refid="RDF Client.classpath"/>
        </java>
    </target>
</project>
```

# ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) Database WOF

Dovrebbe esserci un database Postgres chiamato in `WOF` esecuzione sul localhost alla porta `5432`.

## Creazione delle tabelle del database

Il codice per la creazione della tabella necessaria per il database è il seguente:

``` sql
        create table users
        (
            idUser   varchar(36) primary key,
            isAdmin  numeric(1)  not null,
            name     varchar(50) not null,
            surname  varchar(50) not null,
            nickname varchar(50) not null unique,
            email    varchar(50) not null unique,
            password varchar     not null
        );

        create table matches
        (
            idMatch varchar(36) primary key,
            creationTime varchar(10) not null,
            creationDate varchar(10) not null
        );

        create table phrases
        (
            idPhrase serial primary key,
            phrase 	varchar(60) not null unique,
            theme  	varchar(60) not null
        );

        create table rounds
        (
            roundNumber numeric(1) not null,
            phrase integer references phrases (idPhrase) not null,
            idMatch varchar(36) references matches (idMatch) not null,
            primary key (idMatch, roundNumber)
        );

        create table moves
        (
            idMove   varchar(36) primary key,
            player   varchar(36) references users (idUser) not null,
            movetype varchar(10) not null,
            points  numeric(6)   not null,
            idMatch varchar(36) not null,
            roundNumber   numeric(1)  not null,
            foreign key (idMatch, roundNumber) references rounds
        );

        create table matchwinners
        (
            idMatch  varchar(36) references matches (idMatch) not null,
            player varchar(36) references users (idUser) not null,
            score   numeric(6) not null,
            primary key (idMatch, player)
        );

        create table roundwinners
        (
            idMatch  varchar(36),
            roundNumber   numeric(1),
            player varchar(36) references users (idUser),
            score  numeric(6) not null,
            foreign key (idMatch, roundNumber) references rounds,
            primary key (idMatch, roundNumber, player)
        );

        create table roundparticipants
        (
            idMatch varchar(36),
            roundNumber   numeric(1),
            participant varchar(36) references users (idUser),
            isObserver boolean not null,
            foreign key (idMatch, roundNumber) references rounds,
            primary key (idMatch, roundNumber, participant)
        );
```

## Aggiunta di dati alla tabella `phrases`

```sql
INSERT INTO phrases VALUES (1, 'un fenomeno di costume', 'La moda dei bikini');
INSERT INTO phrases VALUES (2, 'il pane di ramerino', 'Specialità toscana');
INSERT INTO phrases VALUES (3, 'magnetofono', 'Un apparecchio');
INSERT INTO phrases VALUES (4, 'anche i chili sono a contratto', 'Al moulin rouge');
INSERT INTO phrases VALUES (5, 'l''uccellino più puntuale', 'Il cucu');
INSERT INTO phrases VALUES (6, 'il pianeta con piu lune', 'Saturno');
INSERT INTO phrases VALUES (7, 'Il diacono', 'è un futuro sacerdote');
INSERT INTO phrases VALUES (8, 'e il simbolo della croce', 'L''albero di natale');
INSERT INTO phrases VALUES (9, 'lo puoi trovare sul canale quarantesei', 'Cartoonito');
INSERT INTO phrases VALUES (10, 'vinsero la loro battaglia grazie alla loro foga', 'Le amazzoni');
INSERT INTO phrases VALUES (11, 'bast era una divinità con la testa da felino ', 'Nell''antico egitto');
INSERT INTO phrases VALUES (12, 'Francamente me ne infischio', 'Via col vento, 1939');
INSERT INTO phrases VALUES (13, 'Via col vento', 'Francamente me ne infischio');
INSERT INTO phrases VALUES (14, 'Il Padrino', 'Gli farò un offerta che non potrà rifiutare');
INSERT INTO phrases VALUES (15, 'Star Wars', 'Che la Forza sia con te');
INSERT INTO phrases VALUES (16, 'Taxi driver', 'Ma dici a me');
INSERT INTO phrases VALUES (17, 'Apocalypse Now', 'Mi piace l odore del napalm al mattino');
INSERT INTO phrases VALUES (18, 'Il mistero del falco', 'La materia di cui sono fatti i sogni');
INSERT INTO phrases VALUES (19, 'Lucio Dalla', 'dice ciao al 900');
INSERT INTO phrases VALUES (20, 'personaggio biblico', 'Giacobbe');
INSERT INTO phrases VALUES (21, 'Stivali geografici', 'italia arabia nuova zelanda');
INSERT INTO phrases VALUES (22, 'nel medioevo', 'niente tv solo giullari e trovatori ');
INSERT INTO phrases VALUES (23, 'tom e jerry', 'eroi di hanna e barbera');
```

## Aggiunta di utenti alla tabella `users`

```sql
INSERT INTO users VALUES ('ce24665b-be73-4c1d-b541-e3f9830422b7', 1, 'Amminestratore', 'del Gioco', 'admin', 'admin@ruota.it', '2bd01c7bb1d53e454cb7e866c1ba5764');
INSERT INTO users VALUES ('ce24665b-be73-4c1d-b541-e3f9830422b7', 0, 'Tizio', 'Baggio', 'Tizio', 'tizio@ruota.it', '2bd01c7bb1d53e454cb7e866c1ba5764');
INSERT INTO users VALUES ('ce24665b-be73-4c1d-b541-e3f9830422b7', 0, 'Caio', 'Baggio', 'Caio', 'caio@ruota.it', '2bd01c7bb1d53e454cb7e866c1ba5764');
INSERT INTO users VALUES ('ce24665b-be73-4c1d-b541-e3f9830422b7', 0, 'Sempronio', 'Baggio', 'Sempronio', 'sempronio@ruota.it', '2bd01c7bb1d53e454cb7e866c1ba5764');
```

# ![#1589F0](https://via.placeholder.com/15/1589F0/000000?text=+) Avvio del modulo per il Server

```console
utente@computer:~$ cd rdfProject
utente@computer:rdfProject$ ant server
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
utente@computer:~$ cd rdfProject
utente@computer:rdfProject$ javac playerRdF.java
utente@computer:rdfProject$ java playerRdF
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
