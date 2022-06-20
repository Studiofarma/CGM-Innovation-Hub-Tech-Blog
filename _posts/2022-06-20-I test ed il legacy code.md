---
layout: post
title: "3 tipi di Test da conoscere per il Legacy Code"
author: paolo.venturi
categories: [ Legacy Code, Test, Refactoring, Software Development]
image: assets/images/tests_and_legacy_code/0.jpg
excerpt: "Come utilizzare i test automatici per migliorare la propria confidenza nel refactoring del Legacy Code"
---

I test automatici nella produzione del software sono importantissimi: forniscono vantaggi durante e dopo lo sviluppo.

Esistono molte tipologie di test diversi, oggi vedremo i più utili per lavorare con il legacy code.

\
Ma iniziamo dalle basi: perchè i test automatici ci aiutano a sviluppare software?

## Correttezza

I test, da definizione, stabiliscono la correttezza, o meno, del codice.

Quando scriviamo un test stabiliamo delle precondizioni per l'esecuzione del codice da testare.
Tramite le asserzioni andiamo poi a verificare determinate condizioni che si verificano al termine dell'esecuzione.

Sapere che questi requisiti vengono rispettati ci da maggiore confidenza durante lo sviluppo.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/tests_and_legacy_code/1.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://tenor.com/view/rhel1-captain-obvious-thank-you-hero-gif-11964508">
            Tenor
        </a>
    </em>
</center>

## Comprensione

Quando lavoriamo con il legacy code i test ci aiutano a comprendere il codice.

Per definire un test si devono istanziare classi e chiamare funzioni che richiedono dei parametri.

Analizzando i test esistenti abbiamo degli esempi che ci aiutano a capire meglio:

- come istanziare le classi
- comportamenti e parametri delle funzioni
- comportamenti desiderati.

## Velocità

Scrivere i test richiede tempo quindi rallenta lo sviluppo.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/tests_and_legacy_code/2.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://memegenerator.net/instance/66040229/dwight-k-schrute-false">
            Meme Generator
        </a>
    </em>
</center>

\
I test sono un investimento a lungo termine ma anche nel breve forniscono un grosso vantaggio:
restituiscono dei feedback rapidi durante lo sviluppo e questo aiuta a restare concentrati.

Per esempio: dobbiamo testare una funzione che stabilisce se un indirizzo web è corretto o meno.

<script src="https://gist.github.com/paoloventuri91/bbfb194ffcc477cef60f65acb0f71ac1.js"></script>

Senza i test automatici dovremmo eseguire manualmente la funzione con diversi input e confrontare il risultato con quello atteso.

Andando a definire un test come quello seguente, invece, possiamo eseguirlo ogni volta che vogliamo e avremo il risultato in meno di un secondo.

<script src="https://gist.github.com/paoloventuri91/59026a1021316648bdb096fc0130ffee.js"></script>

Inoltre l'esecuzione dei test durante la scrittura del codice semplifica la localizzazione di eventuali errori.

\
Ora che abbiamo compreso l'utilità dei test automatici andiamo ad analizzarne alcune tipologie utili con il legacy code.

## Test di accettazione

I test di accettazione definiscono i comportamenti che deve avere una funzionalità dal punto di vista dell'utente finale.
Sono i clienti che commissionano una modifica o i Product Owner a definire i parametri da rispettare.

I test di accettazione, quindi, sono paragonabili ad un contratto e sono posti ad un livello medio-alto nel codice.
Solo dopo aver eseguito i test con esito positivo la modifica può essere "accettata".

Ad esempio sono test di accettazione:

- asserzioni sul comportamento di un pulsante dell'interfaccia grafica
- asserzioni sulle risposte di una API dato un certo input

Quando dobbiamo lavorare con del codice che non abbiamo scritto ci sentiamo spaesati e insicuri nell'apportare delle modifiche.
Se presenti, i test di accettazione, sono molto utili a ridurre questo disagio.

## Test di unità

Gli unit test sono così definiti perché vanno a testare una singola unità software che può essere una classe o anche solo una funzione.

Dovendo coprire un insieme così piccolo di codice questi test risultano i migliori per localizzare al meglio eventuali errori.

Essendo posti ad un livello basso nel codice sono definiti con un linguaggio tecnico.
Per questo sono poco utili all'utilizzatore finale ma molto pratici per lo sviluppatore.

I test di unità per essere tali devono rispettare una condizione fondamentale: devono essere eseguiti in isolamento.

Non sono test di unità:

- se utilizzano Database
- se sfruttano il File System
- se comunicano attraverso la rete.

In poche parole: i test non devono dipendere da condizioni esterne.

Prendiamo un esempio: il test di una classe che esegue chiamate ad un'API esterna.
I test non dovranno richiamare le API ma simulare una loro esecuzione e risposta.
Utilizzando l'API vera e propria l'esito del test dipenderà da:

- presenza e velocità della connessione di rete
- operatività e stato delle API.

Ma l'obiettivo degli unit test è quello di testare il codice definito in una classe o in un metodo.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/tests_and_legacy_code/3.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://imgflip.com/memegenerator">
            imgflip
        </a>
    </em>
</center>

\
Un'altra caratteristica fondamentale è che i test di unità devono essere veloci.

Restando sull'esempio precedente: i tempi di attesa utilizzando un'API esterna potrebbero essere molto lunghi.
Questo ci porterebbe ad eseguire i test molto meno di frequente aumentando la probabilità di inserire bug.

I test di unità, come i test di accettazione, ci aiutano a comprendere i comportamenti del legacy code.
Rispetto ai test di accettazione, però, vanno ad un livello più basso nella nostra codebase.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/tests_and_legacy_code/4.jpg" width="800">
    <br>
    <em>
        Livelli dei Test
    </em>
</center>

## Test di caratterizzazione

I test di caratterizzazione entrano in gioco quando non esistono i test di unità e di accettazione.

Servono ad aiutarci a comprendere meglio il funzionamento di un programma esistente.
Ci permettono di descrivere i comportamenti attuali di un programma, perciò risultano utilissimi con il legacy code.

Spesso il codice legacy ha caratteristiche che ostacolano i test:

- non fornisce dei valori da testare
- è difficile istanziare classi
- i metodi da richiamare richiedono parametri complessi.

In questi casi si presenta il "Dilemma del Legacy Code":
per poter modificare il codice dobbiamo avere dei test ma per poter definire dei test dobbiamo modificare il codice.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/tests_and_legacy_code/5.gif" width="500">
    <br>
    <em> Photo by
        <a href="https://giphy.com/gifs/theoffice-the-office-tv-show-dw60YIVoqu0MZexouY">
            GIPHY
        </a>
    </em>
</center>

\
Il dilemma si risolve facendo piccole modifiche "sicure" utili a definire i test.
In questo modo, la probabilità di introdurre bug è ridotta al minimo.

Ad esempio: dobbiamo andare a testare il seguente programma che stampa la data odierna.

<script src="https://gist.github.com/paoloventuri91/af2353eff905ec6e7ce12db55e706885.js"></script>

Questo codice non è facilmente testabile perché il risultato non dipende da un input ma dal momento in cui viene eseguito il programma.
Per poter testare la logica quindi dobbiamo fare una modifica che tolga questa dipendenza.

<script src="https://gist.github.com/paoloventuri91/d032294cd5f40f59cef099b38342c782.js"></script>

Grazie alla funzione ```FormatDate``` abbiamo isolato la logica sulla formattazione della data.
Con il parametro ```date```, inoltre, possiamo indicare un input a nostro piacere.

A questo punto scrivere un test risulterà fattibile.

<script src="https://gist.github.com/paoloventuri91/89f3de7c513032c6b0bafc7bfcbafc5d.js"></script>

## Conclusioni

I test automatici sono utilissimi nello sviluppo del software e risultano fondamentali se si deve lavorare con il legacy code.

I test di accettazione e di caratterizzazione coprono il codice ad un livello più alto rispetto ai test di unità.
Entrambi i livelli sono necessari.

Nei prossimi articoli sul legacy code andremo ad analizzare alcune tecniche utili e arriveremo infine alle tecniche di refactoring.

&nbsp;
&nbsp;

***

&nbsp;
&nbsp;

### Riferimenti

- **Working Effectively with Legacy Code** - Michael C. Feathers, 2004 - <https://www.oreilly.com/library/view/working-effectively-with/0131177052>
- **Refactoring: Improving the Design of Existing Code** - Martin Fowler, 1999 - <https://martinfowler.com/books/refactoring.html>
