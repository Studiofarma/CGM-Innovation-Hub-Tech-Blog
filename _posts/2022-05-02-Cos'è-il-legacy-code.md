# Che cos'è il legacy code?

<center>
    <img src="0.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://en.wikipedia.org/wiki/Punched_card#/media/File:Blue-punch-card-front-horiz.png">
            Wikipedia
        </a>
    </em>
</center>

\
Per chi crea software è importante mantenere bassi i tempi e i costi di sviluppo.\
Le tempistiche per implementare una nuova funzionalità, o sistemare un bug, dipendono dalla codebase esistente.\
Stimare l'impegno necessario a modificare del codice semplice e pulito risulta facile, è molto più difficile fare previsioni sul codice legacy.\
Con l'effetto di ottenere stime gonfiate e costi che crescono esponenzialmente.

**Ma cos'è precisamente il legacy code?**\
**Quanti sanno distinguere il legacy code così da evitare di scriverne altro?**

\
Per rispondere a questi dubbi ho fatto quello che farebbe chiunque: ho cercato su Google.

<center>
    <img src="1.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://www.meme-arsenal.com/memes/58b301f6612908a498826a64b4937dd6.jpg">
            Meme-arsenal
        </a>
    </em>
</center>

\
Le definizioni che ho trovato su internet sono tante:

- codice scritto da altri, ereditato (<https://stackoverflow.com/a/4174886>)
- codice "vecchio" (<https://en.wikipedia.org/wiki/Legacy_system>)
- codice che utilizza tecnologie non più supportate (<https://enkonix.com/blog/legacy-code>)
- codice strutturato male (<https://www.oreilly.com/library/view/working-effectively-with/0131177052>)
- codice senza test (<https://understandlegacycode.com/blog/what-is-legacy-code-is-it-code-without-tests>)

<center>
    <img src="2.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://www.meme-arsenal.com/memes/58b301f6612908a498826a64b4937dd6.jpg">
            Meme-arsenal
        </a>
    </em>
</center>

\
Queste definizioni però non spiegano bene cosa sia il legacy code: analizziamole assieme.

&nbsp;
&nbsp;

## Codice scritto da altri, ereditato

Dover lavorare su codice che non abbiamo scritto personalmente è complicato.\
I nomi di variabili e classi potrebbero non essere evocativi o adatti.\
La struttura con cui il codice è organizzato in file e classi non ci risulta famigliare.\
Magari chi lo ha scritto ha utilizzato un pattern che non conosciamo.

\
Questo basta per definire il codice scritto da altri come legacy?

**NO!**

Se chi ha scritto il codice lo ha reso semplice da comprendere allora non può essere definito come legacy code.

&nbsp;
&nbsp;

## Codice "vecchio"

L'età di un codice basta per definirlo legacy?

**NO!**

\
Come per il codice ereditato questa non può bastare come definizione.

Si può stabilire un periodo di tempo oltre il quale il codice diventa vecchio?\
Non è possibile: codice che abbiamo scritto un mese fa potrebbe risultare molto più confuso di codice scritto un anno fa.\
Dipende tutto da quanto lo abbiamo curato e lo abbiamo reso "semplice".

Facciamo un esempio banale.
Un programma per il calcolo dei numeri della sequenza di Fibonacci: il valore di ogni numero corrisponde alla somma dei due numeri precedenti.

<center>
    <img src="3.jpg" width="500">
</center>

Utilizziamo anche due linguaggi di programmazione diversi: il caro vecchio C ed il più moderno C#.

### Sequenza di Fibonacci in C

```C
#include<stdio.h>

int get_fibonacci_number(int index);
int get_index_from_console();
void print_fibonacci_number_on_console(int index);

int main ()
{
    int index = get_index_from_console();
    print_fibonacci_number_on_console(index);

    getchar();
    return 0;
}

int get_fibonacci_number(int index)
{
    if (index <= 1)
        return index;

    return get_fibonacci_number(index-1) + get_fibonacci_number(index-2);
}

int get_index_from_console()
{
    int index;

    printf("Enter Fibonacci sequence index: ");
    scanf("%d", &index);

    return index;
}

void print_fibonacci_number_on_console(int index)
{
    printf("%d", get_fibonacci_number(index));
}
```

### Sequenza di Fibonacci in C\#

```C#
using System;

public class Program
{
    public static void Main()
    {
        Console.WriteLine("Enter Fibonacci sequence index: ");
        int n = Convert.ToInt32(Console.ReadLine());

        int []f = new int[n + 2];
        int i;
        f[0] = 0;
        f[1] = 1;

        for (i = 2; i <= n; i++)
        {
            f[i] = f[i - 1] + f[i - 2];
        }

        Console.WriteLine(f[n]);
    }
}
```

I due programmi fanno la stessa cosa ma le differenze nel codice sono enormi.

Il programma in C sfrutta la ricorsione rendendo molto più leggibile la funzione principale.
Le funzioni e le variabili hanno dei nomi appropriati.\
Al contrario, il programma in C# utilizza un array e un ciclo for per calcolare i numeri della sequenza.\
I nomi delle variabili sono generici e tutta la logica è nella funzione Main.

Da qui è facile capire che l'età del codice o il linguaggio in cui è scritto non bastano per definirlo come legacy.

&nbsp;
&nbsp;

## Codice che utilizza tecnologie non più supportate

Codice che utilizza librerie non più supportate può essere definito come legacy? **NI...**

In questo caso la libreria in questione ci espone a un rischio:
in caso di bug o di funzionalità mancanti le strade percorribili sono solamente due.\
Se la libreria è modificabile possiamo fare noi stessi l'implementazione di cui abbiamo bisogno.\
Altrimenti l'unica soluzione è sostituirla con una libreria che soddisfi le nostre esigenze.

La sostituzione di una libreria può essere una cosa molto complessa.
D'altro canto se la libreria è utilizzata in punti ben isolati il cambio potrebbe risultare quasi indolore.

L'utilizzo di tecnologie obsolete quindi non è sufficiente a definire del codice come legacy.\
Anche se può provocare molti disagi.

&nbsp;
&nbsp;

## Codice strutturato male

Del codice strutturato male, magari un monolite, può essere definito legacy? **SÌ!**

Mancanza di separazione di responsabilità, classi con dipendenze complicate, nomi non evocativi.\
Questi e altri problemi (code smell) rendono il nostro sorgente "debole".

I code smell sono sicuramente uno dei fattori che ci porta a definire del codice come legacy.

&nbsp;
&nbsp;

## Codice senza test

La mancanza dei test basta per definire del codice come legacy? **SÌ!**

Possiamo scrivere codice strutturato benissimo, senza code smell e con librerie molto usate e recenti.\
Ma se la nostra codebase non è coperta da una collezione di test saremo sempre esposti al rischio d'inserire dei bug a ogni modifica.\
I test sono fondamentali per definire i comportamenti desiderati del nostro software.\
Così facendo ci proteggono da possibili errori e ci aiutano a comprendere meglio le logiche del codice che vanno a testare.

&nbsp;
&nbsp;

## Quindi?

Tirando le somme una definizione breve ma efficace potrebbe essere:
**il legacy code è codice non testato difficile da modificare.**

<center>
    <img src="4.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://www.hallofseries.com/wp-content/uploads/2019/01/Immagine-1.png">
            Hall of Series
        </a>
    </em>
</center>

\
Nei prossimi articoli della serie andremo a vedere assieme come lavorare con il legacy code e come renderlo "meno legacy" grazie ai test e al refactoring.

&nbsp;
&nbsp;

***

&nbsp;
&nbsp;

### Riferimenti

- **Working Effectively with Legacy Code** - Michael C. Feathers, 2004 - <https://www.oreilly.com/library/view/working-effectively-with/0131177052>
- **Refactoring: Improving the Design of Existing Code** - Martin Fowler, 1999 - <https://martinfowler.com/books/refactoring.html>
