---
layout: lecture
title: "Panoramica del corso e shell"
date: 2020-01-13
ready: true
video:
  aspect: 56.25
  id: Z56Jmr9Z34Q
---

# Motivazioni

Da informatici sappiamo  che i computer offrono un grande aiuto a svolgere
operazioni ripetitive. Nonostante ciò, troppo spesso ci dimentichiamo che questo
può essere sfruttato anche nell'utilizzo del computer. Abbiamo a disposizione
un'ampia gamma di strumenti che ci permettono di essere più produttivi e di
risolvere problemi complessi che si incontrano durante il lavoro. Solitamente la
maggior parte di noi usa solo marginalmente questi strumenti, magari facendo
ciecamente copia e incolla da qualche sito.

Questa corso ha come obiettivo farvi scoprire questi strumenti e come usarli al
meglio.

Vogliamo insegnarvi ad usare al massimo delle loro potenzialità gli strumenti
che già usate, e mostrarvi nuovi strumenti da aggiungere al vostro bagaglio,
nella speranza di avviarvi all'esplorazione (e magari implementazione) di altri
strumenti. Questo è ciò che pensiamo sia il semestre mancante dalla maggior
parte dei curriculum di informatica.

# Struttura del corso

Il corso è diviso in 11 lezioni da un'ora, ognuna delle quali tratta un
[argomento specifico](/2020/).  Le lezioni sono indipendenti una dall'altra, ma
daremo per scontato che avrete familiarità con gli argomenti delle lezioni
precedenti. Online sono disponibili le note delle lezioni, ma durante le lezioni
ci saranno alcune dimostrazioni interessanti che potrebbero non essere scritte
nelle note. Le lezioni saranno registrate e pubblicate online.

Le lezioni saranno molto dense, nel tentativo di farvi scoprire molto nel poco
tempo a disposizione. Per questo ogni lezione è corredata da degli esercizi che
vi guideranno attraverso i punti chiave della lezione. Dopo ogni lezione ci sarà
ricevimento, durante il quale risponderemo alle vostre domande. Se seguirai da
remoto puoi farci le domande via mail (in inglese) a
[missing-semester@mit.edu](mailto:missing-semester@mit.edu).

Visto il poco tempo a disposizione, non potremmo coprire tutti gli strumenti
allo stesso livello di completezza di un normale corso. Ove possibile vi
indicheremo le risorse che potete usare per approfondire, ma se una cosa
particolare vi stuzzica non esitate a chiederci altri materiali!

# Argomento 1: La Shell

## Cos'è shell?

I computer hanno molte interfacce attraverso cui dar loro comandi; carine
interfacce grafiche, interfacce vocali, e anche attraverso la realtà
virtuale/aumentata. Queste sono comode per l'80% dei casi d'uso, ma sono
solitamente ristrette nelle loro potenzialità: non puoi premere un pulsante se
non c'è o dare un comando vocale non programmato. Per usare al massimo gli
strumenti che il tuo computer ti offre, dobbiamo usare la "vecchia" interfaccia,
ovvero la shell.

In quasi tutte le piattaforme puoi avere accesso ad una shell, in un modo o
nell'altro, e in molte puoi anche scegliere fra una vasta gamma di shell. Mentre
possono cambiare in alcuni dettagli peculiari, il loro _core_ è più o meno lo
stesso: ti consentono di avviare programmi, passargli input, e analizzare il
loro output, in una maniera semi-strutturata.

In questa lezione ci concentreremo sulla Bourne Again SHell (bash): una delle
shell più usate, la cui sintassi è simile a quella di molte altre shell. Per
aprire il _prompt_ di una shell (dove andranno digitati i comandi), occorre un
_terminale_. Molto probabilmente il tuo dispositivo ne ha già uno, altrimenti
puoi facilmente installarlo.

## Usare la shell

Quando avvii il terminale, vedrai un _prompt_ che solitamente assomiglia a
questo:

```console
missing:~$ 
```

Questa è l'interfaccia testuale principale della shell. Ti dice che stai usando
la macchina di nome `missing` e che la tua cartella di lavoro corrente è `~` (un
alias per indicare la cartella personale dell'utente, la _home_). Il dollaro
indica che sei un utente standard (non _root_, ne parleremo dopo). Dopo questo
prompt puoi digitare un _comando_, che verrà interpretato dalla shell. Il
comando più semplice che puoi dare è eseguire un programma:

```console
missing:~$ date
Fri 10 Jan 2020 11:49:31 AM EST
missing:~$ 
```

Qui abbiamo eseguito il programma `date`, che sorprendentemente stampa a video
la data e l'ora corrente. La shell dopodiché mostra un altro prompt in attesa
del prossimo comando. Possiamo anche eseguire comandi con _argomenti_:

```console
missing:~$ echo hello
hello
```

In questo caso abbiamo detto alla shell di eseguire il programma `echo` con
`hello` come argomento. Il programma `echo` semplicemente stampa a video i suoi
argomenti. La shell interpreta il comando e i suoi argomenti sperandoli con uno
spazio, e poi avvia il programma indicato dalla prima parola, passandogli come
argomento ogni parola successiva. Se vuoi passare un argomento che contiene uno
spazio o altri caratteri speciali (ad esempio la cartella "Foto personali"),
puoi sia racchiudere l'argomento fra virgolette singole (`'Foto personali'`) o 
doppie (`"foto personali"`), oppure usare la sequenza di _escape_ per lo spazio 
(`Foto\ personali`).

Ma come fa la shell a sapere dove si trovano i  programmi?
La shell è un ambiente di programmazione, come Python o Ruby, e ha quindi le
variabili, le condizioni, i cicli e le funzioni (vedremo tutte queste cose nella
prossima lezione!). Quando avvi un comando nella shell, non stai facendo altro
che scrivere un piccolo pezzo di codice che la tua shell interpreta. Se alla
shell viene chiesto di eseguire un comando che non rientra nelle sue _keyword_,
allora legge una _variabile d'ambiente_ chiamata `$PATH` che elenca le cartelle
dove la shell deve cercare i programmi richiesti:


```console
missing:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
missing:~$ type echo
/bin/echo
missing:~$ /bin/echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

Quando avvii il comando `echo`, la shell capisce che deve eseguire il programma
`echo` e lo cerca attraverso le cartelle elencate (separate da `:`) nella
variabile `$PATH`. Quando lo trova lo esegue (assumendo che il file abbia i
permessi di esecuzione; ne parleremo più avanti). Possiamo trovare quale file
venga eseguito per un dato programma usando il comando `type` (o il suo
antenato, `which`). Possiamo eludere `$PATH` inserendo direttamente il percorso
completo al file che vogliamo eseguire.

## Muoversi nella shell

Un percorso nella shell è una lista di cartelle separate da `/` (o `\` su
Windows). Negli ambienti Unix-like (come GNU/Linux e macOS) il percorso `/`
rappresenta la radice (root) del _file system_, dentro la quale si trovano tutti
i file e le cartelle, mentre in Windows c'è una radice diversa per ogni
partizione (ad esempio `C:\`). In questo corso supporremo che stiate usando un
file system Unix-like. Un percorso che iniza con `/` è detto _assoluto_. Ogni
altro percorso è detto _relativo_. I percorsi relativi sono relativi all'attuale
cartella di lavoro, che può essere visualizzata con il comando `pwd` (print
working directory) e cambiata con `cd` (change directory). In un percorso `.` si
riferisce alla cartella corrente, mentre `..` alla cartella padre:

```console
missing:~$ pwd
/home/missing
missing:~$ cd /home
missing:/home$ pwd
/home
missing:/home$ cd ..
missing:/$ pwd
/
missing:/$ cd ./home
missing:/home$ pwd
/home
missing:/home$ cd missing
missing:~$ pwd
/home/missing
missing:~$ ../../bin/echo hello
hello
```

Notate come il prompt ci tenga informati sulla cartella in cui stiamo lavorando.
Il prompt può essere personalizzato per mostrare molte altre informazioni utili,
che andremo a vedere più avanti.

In generale, quando avviamo un programma, questo opera nella cartella corrente a
meno che non specifichiamo diversamente. Ad esempio aprirà e/o creerà file nella
cartella in cui ci troviamo quando viene eseguito.

Per vedere il contenuto di una cartella si usa il comando `ls`:

```console
missing:~$ ls
missing:~$ cd ..
missing:/home$ ls
missing
missing:/home$ cd ..
missing:/$ ls
bin
boot
dev
etc
home
...
```

`ls` mostra il contenuto della cartella corrente o della cartella che gli viene
passata come argomento. La maggior parte dei comandi accetta dei _flag_ e
opzioni (flag con un valore associato), che iniziano con un trattino, i quali
modificano il loro comportamento. Di solito, avviando un programma col flag `-h`
o `--help` stamperà a video un riassunto dei flag e delle opzioni disponibili.
Per esempio `ls --help` ci dirà:

```
  -l                         use a long listing format
```

```console
missing:~$ ls -l /home
drwxr-xr-x 1 missing  users  4096 Jun 15  2019 missing
```

Questo ci darà molte informazioni in più riguardo ai file e alle cartelle
presenti. La `d` all'inizio della riga ci dice che si tratta di una cartella
(directory). Poi ci sono 3 gruppi da 3 caratteri (`rwx`) indicanti i permessi
che il proprietario del file (`missing`), il gruppo proprietario (`users`), e
tutti gli altri hanno rispettivamente sull'elemento. Un `-` indica che il
permesso non è concesso. Nell'esempio solo il proprietario ha i permessi di
scrittura (`w`riting; ovvero aggiungere/rimuovere file). Per accedere ad una
cartella bisogna avere i permessi di esecuzione (e`x`ecute) sulla
stessa, nonché su tutte le cartelle genitore. Per elencare i file bisogna avere
i permessi di lettura (`r`eading) sulla cartella. Per i file i permessi sono
più intuitivi. Notate come quasi tutti i file in `/bin` siano eseguibili da
chiunque in modo che tutti possano avviare questi programmi.

Molti altri programmi utili da conoscere a questo punto sono `mv` per rinominare
e spostare file, `cp` per copiarli e `mkdir` per creare cartelle.

Se vuoi più informazioni su un programma e i suoi argomenti, usa il programma
`man` che fornisce i manuali della maggior parte dei programmi a linea di
comando. Per uscire dal manuale premi `q`.

```console
missing:~$ man ls
```

## Connettere programmi

Nella shell i programmi hanno due canali (stream) principali che possono usare:
quello di input e quello di output. Normalmente entrambi sono associati al
terminale, ovvero la tua tastiera come input e lo schermo com output. Nonostante
ciò è possibile reindirizzare questi stream.

Il modo più semplice per farlo è `< file` e `> file`. Questi ti permettono di
reindirizzare rispettivamente l'input e l'output di un programma su un file:

```console
missing:~$ echo hello > hello.txt
missing:~$ cat hello.txt
hello
missing:~$ cat < hello.txt
hello
missing:~$ cat < hello.txt > hello2.txt
missing:~$ cat hello2.txt
hello
```

Nell'esempio abbiamo usato `cat`, un programma che con`cat`ena file e li stampa
in output, prendendoli dai suoi argomenti o, se non ci sono, dal suo input.

Puoi usare anche `>>` per aggiungere ad un file (`>` cancella il file se
esiste già).

Inoltre l'operatore _pipe_ (`|`) permette di concatenare l'output di un
programma all'input del successivo:

```console
missing:~$ ls -l / | tail -n1
drwxr-xr-x 1 root  root  4096 Jun 20  2019 var
missing:~$ curl --head --silent google.com | grep --ignore-case content-length | cut --delimiter=' ' -f2
219
```

Vedremo meglio come usare le pipe nelle lezione sulla manipolazione dei dati.

## Uno strumento versatile e potente

Nella maggior parte dei sistemi Unix-like c'è un utente speciale: root. Dovreste
averlo visto nei file elencati nell'esempio qua sopra. Questo utente è al di
sopra di quasi tutte le restrizioni e può creare, leggere, modificare e
cancellare ogni file del sistema. Per questo non bisogna entrare nel sistema
come root in quanto sarebbe facilissimo rompere qualcosa. Tuttavia a volte è
necessario agire come root, e si può fare usando il comando `sudo` (super user,
root, do). Quando un programma ti avvisa che non hai i permessi per fare
qualcosa normalmente significa che necessiti di farlo tramite sudo, ma accertati
sempre che sia veramente il modo giusto di farlo.

Ad esempio per scrivere sul file system `sysfs` (montato in `/sys`) è necessario
essere root. `sysfs` espone molti parametri del kernel come file, permettendone
una facile modifica. **Nota che sysfs non è disponibile su Window e macOS**

Per esempio la luminosità dello schermo di un portatile è disponibile su un file
chiamato `brightness` all'interno di

```
/sys/class/backlight
```

Scrivendo su questo file si può cambiare al volo la luminosità.
Istintivamente potreste fare una cosa simile:

```console
$ sudo find -L /sys/class/backlight -maxdepth 2 -name '*brightness*'
/sys/class/backlight/thinkpad_screen/brightness
$ cd /sys/class/backlight/thinkpad_screen
$ sudo echo 3 > brightness
An error occurred while redirecting file 'brightness'
open: Permission denied
```

Questo errore potrebbe sorprendere più di qualcuno. Dopotutto abbiamo avviato il
comando come `sudo`! Questa è una cosa importante da sapere sulla shell. Le
operazioni come `|`, `>`, `<` sono eseguite dalla shell, non dal singolo
programma. `echo` e gli altri non sono a conoscenza di `|`: loro semplicemente
leggono e scrivono dai loro stream, qualsiasi siano. Nell'esempio sopra la
shell, che è autenticata come l'utente, tenta di aprire il file brightness in
scrittura, prima che venga impostato come output di `sudo echo`, ma non può
farlo perché non è stata eseguita da root. Sapendo ciò possiamo risolvere così:

```console
$ echo 3 | sudo tee brightness
```

Siccome è il comando `tee` (che reindirizza il suo input sul file che prende
come argomento) ad aprire il file in scrittura, ed è avviato come `root`, il
tutto funziona. Puoi controllare in questo modo tutto ciò che si trova sotto
`/sys`, come lo stato di vari LED (il percorso varia da macchina a mancchina):

```console
$ echo 1 | sudo tee /sys/class/leds/input6::scrolllock/brightness
```

# Prossimi passi

A questo punto sai usare la shell abbastanza da poter completare semplici
azioni. Dovresti essere in grado di muoverti fra le cartelle per trovare i file
su cui lavorare e usare le funzionalità base dei programmi. Nella prossima
lezione vedremo come completare e automatizzare task più complesse con la shell
e molti programmi a linea di comando.

# Esercizi

Tutte le lezioni in questo corso sono corredate da una serie di esercizi. Alcuni
vi daranno alcuni compiti specifici da svolgere, mentre altri sono più liberi,
come "prova ad usare i programmi X e Y". Raccomandiamo fortemente di provali.

Non abbiamo scritto le soluzioni degli esercizi. Se ti blocchi in un punto
particolare, scrivici pure una mail descrivendo cosa hai provato a fare e
proveremo ad aiutarti.

 1. Per questo corso dovrai usare una shell Unix come Bash o ZSH. Se usi
    GNU/Linux o macOS non dovrai fare nulla di speciale; se invece usi
    Windows non potrai usare cmd.exe, né la PowerShell, bensì dovrai usare il
    [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/)
    o una macchina virtuale. Per essere sicuro di star usando una shell
    appropiata puoi provare il comando `echo $SHELL`:  se stampa qualcosa come
    `/bin/bash` o `/usr/bin/zsh`, questo significa che stai eseguendo il
    programma corretto.
 1. Crea una nuova cartella chiamata `missing` dentro `/tmp`.
 1. Scopri cosa fa il comando `touch`. Ricorda che `man` è tuo amico.
 1. Usa `touch` per creare un nuovo file chiamato `semester` nella cartella
    `missing`.
 1. Scrivi quanto segue, una linea alla volta, nel file appena creato:
    ```
    #!/bin/sh
    curl --head --silent https://missing.csail.mit.edu
    ```
    La prima linea potrebbe essere complicata da inserire. Infatti in bash i
    commenti iniziano con `#` e `!` ha un significato speciale anche fra
    doppie virgolette (`"`). Per ovviare a questo problema vanno usate le
    singole virgolette (`'`). Per più informazioni [guarda](https://www.gnu.org/software/bash/manual/html_node/Quoting.html) il manuale di bash
 1. Prova ad eseguire il file (digita `./semester` e premi invio). Cerca di
    capire, con l'aiuto di `ls`, perché non funzioni (suggerimento: controlla i
    bit dei permessi).
 1. Avvia il comando avviando esplicitamente l'interprete `sh` passandogli il
    file `semester` come argomento (`sh semester`). Perché così funzona e prima
    non andava?
 1. Scopri come funziona il programma `chmod`.
 1. Usa `chmod` per rendere possibile eseguire il comando `./semester` evitando
    di dover eseguire `sh semester`. Come fa la shell a capire che il file deve
    essere interpretato da `sh`? Guarda questa pagina riguardo gli
    [shebang](https://it.wikipedia.org/wiki/Shabang) per maggiori informazioni.
 1. Usa `|` e `>` per scrivere la data di ultima modifica restituita da
    `semester` in un file chiamato `ultima-modifica.txt` nella tua cartella
    home.
 1. Scrivi un comando che legge la percentuale di batteria del tuo portatile o
    la temperatura della CPU del tuo fisso, leggendole da `/sys`. Nota: se usi
    macOS non puoi fare questo esercizio.
