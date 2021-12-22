---
layout: lecture
title: "Strumenti della shell e script"
date: 2020-01-14
ready: true
video:
  aspect: 56.25
  id: kgII-YWo3Zw
---

In questa lezione vedremo un'introduzione all'uso della shell come linguaggio
di scripting, insieme ad degli strumenti che si usano frequentemente sulla
linea di comando.

# Shell Scripting

Fino a qua abbiamo visto come eseguire comandi nella shell e come concatenarli.
Però in molti scenari potreste volere fare delle cose in più, come delle
esecuzioni condizionate o dei cicli.

Quasi tutte le shell hanno il loro linguaggio di scripting con variabili,
controlli del flusso, e la loro sintassi.
La peculiarità di questi linguaggi è che sono ottimizzati per eseguire
operazioni legate alla riga di comando.
Quindi operazioni come concatenazione di comandi, salvataggio dei risultati su
file e lettura dallo standard input sono primitive importanti, che consentono
una scrittura più facilitata di script, rispetto a linguaggi più generici.
In questa lezione tratteremo il linguaggio di scripting di bash, che è il più
comune.

Per assegnare ad una variabile si usa la sintassi `nome=valore` e l'accesso al
valore della variabile con `$nome`.
Prestate attenzione al fatto che `nome = valore` non funziona perché viene
interpretato come la chiamata al comando `foo` con argomenti `=` e `valore`.
In generale in bash lo spazio separa gli argomenti. Questo comportamento può
sembrare strano all'inizio, quindi fate attenzione.

Le stringhe di testo possono essere definite sia con `'` che con `"`, ma non
sono equivalenti.
Le stringhe delimitate dai singoli apici sono stringhe letterali, mentre le
stringhe con le doppie virgolette supportano l'espansione delle variabili.

```bash
nome=valore
echo "$nome"
# stampa valore
echo '$nome'
# stampa $nome
```

Come nella maggior parte dei linguaggi bash supporta i controlli di flusso
(`if`, `case`, `while` e `for`).
Allo stesso modo in bash si possono definire delle funzioni che prendono degli
argomenti e possono operare con essi. Qui vediamo un esempio di una funzione che
crea una cartella e si sposta all'interno di essa.

```bash
mcd () {
    mkdir -p "$1"
    cd "$1"
}
```

Qui `$1` è il primo argomento della funzione.
A differenza degli altri linguaggi, bash usa delle variabili speciali per
rappresentare gli argomenti, i codici di errore e altro. Segue una lista di
quelle più comuni. Un elenco più completo può essere trovato [qui](https://tldp.org/LDP/abs/html/special-chars.html).
- `$0` - Nome dello script
- da `$1` a `$9` - Argomenti dello script/funzione. `$1` è il primo e così via.
- `$@` - Tutti gli argomenti
- `$#` - Numero di argomenti
- `$?` - Codice di ritorno del comando precedente
- `$$` - PID (numero identificativo del processo) dello script corrente
- `!!` - Ultimo comando eseguito, compresi i suoi argomenti. Spesso viene usato
         quando ci si accorge di aver eseguito un'operazione per cui non si
         hanno i permessi: per rieseguirla come root basta dare `sudo !!`.
- `!*` - Argomenti dell'ultimo comando eseguito.
- `$_` - Ultimo argomento dell'ultimo comando eseguito.

I comandi solitamente ritornano un output mediante `STOUT` e gli eventuali
errori mediante `STDERR`, più un codice di ritorno.
Quest'ultimo assume il valore 0 se tutto è andato a buon fine, altrimenti un
altro numero che indica che c'è stato un errore.

I codici di ritorno possono essere usati per l'esecuzione condizionati di altri
comandi usando gli operatori logici `&&` (e) e `||` (o), entrambi
[cortocircuitati](https://it.wikipedia.org/wiki/Valutazione_a_corto_circuito).
Più comandi possono essere scritti sulla stessa riga, separati da punti e
virgola `;`.
Il comando `true` ha sempre 0 come codice di ritorno, mentre `false` sempre 1.
Vediamo alcuni esempi.

```bash
false || echo "Oops, fallito"
# Oops, fallito

true || echo "Non verrà stampato"
#

true && echo "Tutto a posto"
# Tutto a posto

false && echo "Non verrà stampato"
#

true ; echo "Questo verrà sempre eseguito"
# Questo verrà sempre eseguito

false ; echo "Questo verrà sempre eseguito"
# Questo verrà sempre eseguito
```

Un'altra operazione molto comune è voler salvare in una variabile l'output di un
comando. Questo è possibile con la _sostituzione di comando_.
Ovunque si inserisca `$( CMD )` la shell eseguirà `CMD` e sostituirà questa
sintassi con l'output dell'esecuzione.
Ad esempio scrivendo `for file in $(ls)`, la shell prima chiamerà `ls` e poi
itererà fra i file elencati dal comando.  
Simile, ma meno nota, è la _sostituzione di processo_, `<( CMD )`. Questa esegue
sempre il comando `CMD` e reindirizza il suo output su un file temporaneo, il
cui percorso sostituirà la sintassi `<()` nello script. Questa è molto utile per
comandi che si aspettano il percorso di un file come argomento. Ad esempio `diff
<(ls pippo) <(ls pluto)` mostrerà le differenze fra l'elenco dei file presenti
nelle cartelle `pippo` e `pluto`.

Siccome vi abbiamo fornito molte informazioni in poche righe, vediamo ora un
esempio che ci mostri l'uso di questi strumenti. Questo script itera fra i suoi
argomenti, usa `grep` alla ricerca della stringa `paperino` e, se non lo trova,
lo aggiunge come commento.

```bash
#!/bin/bash

echo "Avvio del programma in data $(date)" # La data sarà sostituita

echo "Esecuzione del programma $0 con $# argomenti e pid $$"

for file in "$@"; do
    grep paperino "$file" > /dev/null 2> /dev/null
    # Se il pattern non viene trovato, grep esce con codice 1
    # Reindirizziamo STDOUT e STDERR in un registro nullo
    # dato che non ne siamo interessati
    if [[ $? -ne 0 ]]; then
        echo "Il file $file non contiene 'paperino', aggiungiamone uno"
        echo "# paperino" >> "$file"
    fi
done
```

Nell'if abbiamo testato se `$?` fosse diverso da 0. Bash implementa molti
comparatori di questo tipo: un elenco dettagliato può essere reperito nella
pagina di manuale di 
[`test`](https://www.man7.org/linux/man-pages/man1/test.1.html).
Quando si eseguono comparazione in bash è però preferibile usare le doppie
parentesi quadre `[[ ]]` al posto delle singole `[ ]`. Così i rischi di
commettere errori saranno minori, tuttavia il codice non sarà retrocompatibile
con `sh`. Maggiori informazioni possono essere trovate 
[qui](http://mywiki.wooledge.org/BashFAQ/031).

Lanciando uno script spesso ci si trova a passare una serie di argomenti simili
fra loro. Bash ha dei modi per facilitare l'inserimento di tali argomenti,
espandendo delle espressioni chiamate _glob_.
- Metacaratteri - Si possono usare i metacaratteri `?` e `*` per rappresentare
  rispettivamente uno o molti caratteri qualsiasi. Ad esempio, dati i file
  `pippo`, `pippo1`, `pippo2`, `pippo10` e `pluto`, il comando `rm pippo?`
  cancellerà `pippo1` e `pippo2`, mentre `rm pippo*` cancellerà tutti i file
  meno `pluto`.
- Parentesi graffe `{}` - Quando c'è una sottostringa comune in una serie di
  comandi si possono usare le parentesi graffe per racchiudere un elenco,
  separato da virgole, delle parti divergenti del comando. Questo è molto utile
  quando devi spostare o convertire file.

```bash
convert immagine.{png,jpg}
# Verrà espanso in
convert immagine.png immagine.jpg
cp /percorso/al/progetto/{pippo,pluto,paperino}.sh /nuovo/percorso
# Verrà espanso in
cp /percorso/al/progetto/pippo.sh /percorso/al/progetto/pluto.sh /percorso/al/progetto/paperino.sh /nuovo/percorso
# Le tecniche di glob possono anche essere combinate
mv *{.py,.sh} cartella
# Sposterà tutti i file *.py e *.sh
mkdir pinco pallino
# Questo comando creerà i file pinco/a, pinco/b , ..., pinco/h, pallino/a,
# pallino/b, ..., pallino/h
touch {pinco,pallino}/{a..h}
touch pinco/x pallino/y
# Mostrerà le differenze fra i file in pinco e qulli in pallino
diff <(ls pinco) <(ls pallino)
# Stamperà
# < x
# ---
# > y
```

Scriver script in `bash` può essere poco intuitivo. Esistono però alcuni
strumenti, come [shellcheck](https://github.com/koalaman/shellcheck), che
possono aiutare a trovare errori.

È importante notare che gli script non necessitano di essere scritti in bash per
essere invocati nel terminale. Ad esempio, qui vediamo un semplice script Python
che stampa i suoi argomenti in ordine inverso:

```python
#!/usr/local/bin/python
import sys
for arg in reversed(sys.argv[1:]):
    print(arg)
```

Il kernel sa che deve eseguire questo script mediante un interprete python anzi
che un comando della shell grazie allo
[shebang](https://it.wikipedia.org/wiki/Shabang) in cima allo script.
È buona prassi scrivere lo shebang usando il comando
[`env`](https://www.man7.org/linux/man-pages/man1/env.1.html) che andrà alla
ricerca del comando all'interno del sistema, aumentando così la portabilità
dello script (infatti specificando un percorso assoluto potrebbe non funzionare
se i comandi si trovano in cartelle diverse su sistemi diversi). Il comando
`env` per trovare il percorso assoluto dei comandi usa la variabile d'ambiente
`PATH` che abbiamo introdotto nella prima lezione.
Quindi nell'esempio qua sopra sarebbe stato più corretto scrivere
`#!/usr/bin/env python`.

Alcune differenze importanti fra le funzioni della shell e gli script sono:
- Le funzioni devono essere scritte nello stesso linguaggio della shell, mentre
  gli script possono essere scritti in qualsiasi linguaggio. Per questo inserire
  lo shebang è importante.
- Le funzioni sono caricate una volta, alla loro definizione. Gli script invece
  sono caricati ad ogni loro esecuzione. Per questo le funzioni sono leggermente
  più veloci.
- Le funzioni vengono eseguite nell'ambiente della shell corrente, mentre gli
  script vengono eseguiti in un processo a se stante. Quindi le funzioni possono
  modificare le variabili d'ambiente (ad esempio la cartella di lavoro), mentre
  gli script non possono farlo. Agli script vengono passate per valore le
  variabili d'ambiente che vengono esportate usando
  [`export`](https://www.man7.org/linux/man-pages/man1/export.1p.html)
- Come in ogni linguaggio, le funzioni sono un costrutto potente per scrivere
  codice modulare, riutilizzabile e chiaro. Spesso gli script shell includono
  funzioni al loro interno, utilizzate dallo script stesso.

# Strumenti della shell

## Capire come usare i comandi

A questo punto potreste chiedervi come capire che flag usare nei comandi, come
`ls -l`, `mv -i` e `mkdir -p`. Più in generale, dato un comando, come si può
sapere cosa fa e quali opzioni accetta? Si può fare una ricerca su internet, ma
esiste anche uno strumento già integrato nei sistemi Unix-like.

Come abbiamo visto nella prima lezione, l'approccio più semplice è aggiungere il
flag `-h` o `--help` alla chiamata del programma. Per avere una descrizione più
ampia possiamo invece usare il comando `man` (manuale). Questo comando fornisce
le pagine di manuale per il comando specificato. Ad esempio `man rm` mostrerà il
comportamento di `rm` e tutti glia argomenti da lui accettati. Anche per molti
comandi non nativi esiste la pagina di manuale, se l'autore del programma le ha
previste. Per strumenti interattivi (ad esempio quelli basati su ncurses)
la pagina di aiuto può essere mostrata usando il comando `:help` o `?`.

A volte le pagine di manuale sono molto prolisse, rendendo difficile capire che
flag e opzioni usare per i casi comuni. Esiste però 
[TLDR pages](https://tldr.sh/) che offre dei semplici esempi degli usi più
comuni dei programmi. Ad esempio può essere comodo usarlo per i comandi
[`tar`](https://tldr.ostera.io/tar) e [`ffmpeg`](https://tldr.ostera.io/ffmpeg).


## Trovare i file

Uno dei compiti più ripetitivi di un programmatore è trovare i propri file e
cartelle. In tutti i sistemi Unix-like è preinstallato il pacchetto
[`find`](https://www.man7.org/linux/man-pages/man1/find.1.html), uno strumento
ben fatto per trovare velocemente i file. `find` cerca ricorsivamente i file che
rispondono a certi criteri, ad esempio:

```bash
# Trova tutte le cartelle (directory) chiamate src
find . -name src -type d
# Trova tutti i file python che hanno una cartella
# chiamata test nel loro percorso
find . -path '*/test/*.py' -type f
# Trova tutti i file modificati nell'ultimo giorno
find . -mtime -1
# Trova tutti i file compressi di dimensione fra 500k e 10M
find . -size +500k -size -10M -name '*.tar.gz'
```
Oltre ad elencare file, find può anche eseguire azione su di loro. Questo può
essere incredibilmente di aiuto per semplificare molte attività che altrimenti
andrebbero svolte manualmente.
```bash
# Cancella tutti i file con estensione .tmp
find . -name '*.tmp' -exec rm {} \;
# Trova tutti i PNG e convertili in JPG
find . -name '*.png' -exec convert {} {}.jpg \;
```

Nonostante `find` sia il più diffuso strumento per questo scopo, la sua sintassi
è difficile da ricordare. Ad esempio solo per trovare i file il cui nome
contiene un certo pattern `PATTERN` bisogna eseguire `find -name '*PATTERN*'`
(o `-iname` se si vuole un matching _case insensitive_).
Si possono creare degli alias per questi scenari comuni, ma è bene anche
esplorare alternative. Infatti nella shell i comandi non sono altro che
programmi installati, quindi puoi trovare (o scrivere) programmi di rimpiazzo.
Ad esempio, [`fd`](https://github.com/sharkdp/fd) è un'alternativa semplice,
veloce e user-friendly a `find`. Offre alcune opzioni di default molto carine
come l'output a colori, l'uso delle espressioni regolari e il supporto
all'Unicode. Ha anche, secondo me, una sintassi molto più intuitiva: ad esempio
per cercare il pattern `PATTERN` è sufficiente eseguire `fd PATTERN`.

La maggior parte di voi penserà che `find` e `fd` siano ottimi strumenti, ma
alcuni potrebbero chiedersi dell'efficienza del cercare ricorsivamente i file
ogni volta, opposto ad un sistema di base di dati indicizzata per ricerche più
rapide. Questo è quello che fa
[`locate`](https://www.man7.org/linux/man-pages/man1/locate.1.html).
Questo comando usa un database aggiornabile con
[`updatedb`](https://www.man7.org/linux/man-pages/man1/updatedb.1.html).
In molti sistemi il database è aggiornato giornalmente con 
[`cron`](https://www.man7.org/linux/man-pages/man8/cron.8.html).

## Trovare codice

Trovare i file mediante il loro nome è utile, ma spesso è più utile cercarli
mediante il loro _contenuto_. Uno scenario comune è trovare tutti i file che
contengono un pattern, e il punto esatto dove compare in essi. A questo scopo
molti sistemi Unix-like forniscono 
[`grep`](https://www.man7.org/linux/man-pages/man1/grep.1.html), un tool
generico per la ricerca di pattern nel testo in input.
`grep` è uno strumento molto utile, che andremo a coprire in maggior dettaglio
nella lezione sulla manipolazione dei dati.

Per il momento ci basta sapere che `grep` ha molti flag che lo rendono
versatile. Quelli che uso più spesso sono `-C` per stampare anche il
**c**ontesto del match e `-v` per in**v**ertire il match (ovvero stamapre le
righe che non corrispondono). Ad esempio `grep -C 5` stamperà anche 5 righe
prima e dopo il match. Se si vuole fare piccole ricerche su più file, risulta
utile il flag `-R` che **r**icorsivamente esplora le cartelle alla ricerca di
file il cui contenuto corrisponda al pattern.

Ma `grep -R` può essere migliorato in molti modi, come ignorando le cartelle
`.git`, sfruttando il multi-threading, ...  
Negli anni sono state sviluppate molte alternative a `grep`, inclusi
[ack](https://beyondgrep.com/),
[ag](https://github.com/ggreer/the_silver_searcher)
e [rg](https://github.com/BurntSushi/ripgrep).
Sono tutti strumenti fantastici e offrono più o meno le stesse funzionalità.
Attualmente sto usando ripgrep (`rg`), dato che è molto veloce ed intuitivo.
Ecco alcuni esempi:
```bash
# Trova tutti i file python dove ho usato la libreria requests
rg -t py 'import requests'
# Trova tutti i file (inclusi quelli nascosti) senza shebang
rg -u --files-without-match "^#!"
# Trova tutti i match di pippo e stampa le 5 righe seguenti
rg pippo -A 5
# Stampa le statistiche sui macth (numero di righe e file che corrispondono)
rg --stats PATTERN
```

Notate che, come con `find`/`fd`, è importante che sappiate che questi problemi
si possano facilmente risolvere con questi strumenti, mentre saper usare il tool
specifico non ha la stessa importanza.

## Trovare comandi della shell

Fino a qui abbiamo visto come cercare file e codice, ma iniziando ad usare di
più la shell vi accorgerete che vi sarà utile trovare un specifico comando che
avete digitato tempo fa. La prima cosa da sapere è che la freccia in su scorre
la cronologia dei comandi.

Il comando `history` ti permette invece un accesso puntuale alla cronologia.
Infatti stampa a video tutta la cronologia dei comandi della shell. Per trovare
quello che cerchiamo possiamo concatenarlo col comando `grep` alla ricerca di
pattern: `history | grep pattern` stamperà i comandi che corrispondo al pattern
richiesto.

Nella maggior parte delle shell si può premere `Ctrl+R` per effettuare una
ricerca nella cronologia digitando una sottostringa del comando desiderato.
Ripremendo la combinazione di tasti al ricerca scorrerà all'indietro
(**r**everse). Questo comportamento può essere abilitato di default alle
freccette su e giù in
[zsh](https://github.com/zsh-users/zsh-history-substring-search).  
Interessante è
[fzf](https://github.com/junegunn/fzf/wiki/Configuring-shell-key-bindings#ctrl-r),
un _fuzzy finder_ che effettua _ricerche approssimative_, che ha dei _binding_
per `Ctrl+R` i quali permettono una ricerca approssimata nella cronologia dei
comandi.

Un altro bel trucchetto sono gli auto-suggerimenti dei comandi basati sulla
cronologia. Introdotti nella shell [fish](https://fishshell.com/) effettuano un
autocompletamento dinamico dei comandi mentre vengono digitati, basati su
comandi recenti con lo stesso prefisso. Possono essere abilitati in
[zsh](https://github.com/zsh-users/zsh-autosuggestions).

Il comportamento della cronologia può essere modificato, come ad esempio
disabilitando l'inclusione di comandi che iniziano per spazio. Questo è molto
comodo quando stai digitando comandi che contengono password o altre
informazioni sensibili da non salvare in chiaro.
Per fare ciò aggiungi `HISTCONTROL=ignorespace` al file di configurazione
`.bashrc` o, se usi zsh, `setopt HIST_IGNORE_SPACE` al `.zshrc`.
Se dimentichi di inserire lo spazio iniziale puoi sempre cancellare manualmente
i comandi dal file `.bash_history` o `.zhistory`.

## Muoversi fra le cartelle

Finora abbiamo supposto che ti trovavi già nella cartella dove dovevi compiere
le operazioni. Ma come spostarsi velocemente in una cartella? Ci sono molti
semplici modi per farlo, come scrivere degli alias per la shell o creare
collegamenti simbolici con
[ln -s](https://www.man7.org/linux/man-pages/man1/ln.1.html), usare la ricerca
nella cronologia, ma gli sviluppatori hanno trovato dei modi intelligenti e
sofisticati per farlo.

Per trovare file e cartelle recenti e/o frequenti si può usare degli strumenti
come [`fasd`](https://github.com/clvv/fasd)
e [`autojump`](https://github.com/wting/autojump).
Il primo ordina file e cartelle per
[_frecency_](https://web.archive.org/web/20210421120120/https://developer.mozilla.org/en-US/docs/Mozilla/Tech/Places/Frecency_algorithm),
ovvero per _frequenza_ (*fre*qunce) e _data_ (re*cency*).
`fasd` offre anche un comando `z` che permette di spostarsi velocemente in una
cartella inserendo una sottostringa di una cartella frequente. Ad esempio,
se vai spesso in `/home/utente/file/progetto_bello`, puoi arrivarci
semplicemente con `z bello`. Usando autojump si può fare la stessa cosa, usando
però il comando `j`.

Esisto degli strumenti più complessi che offrono una visuale veloce della
struttura delle cartelle: [`tree`](https://linux.die.net/man/1/tree),
[`broot`](https://github.com/Canop/broot) o anche un gestore dei file completo
come [`nnn`](https://github.com/jarun/nnn)
o [`ranger`](https://github.com/ranger/ranger).

# Esercizi

1. Leggi [`man ls`](https://www.man7.org/linux/man-pages/man1/ls.1.html) e
   scrivi un comando `ls` che listi i file nella seguente maniera:

    - Includa tutti i file, compresi quelli nascosti
    - Le dimensioni sono mostrate in un formato umanamente leggibile
      (ad esempio 454M anzi che 454279954)
    - I file sono ordinati per data
    - L'output è colorato

    Ecco un output di esempio:

    ```
    -rw-r--r--   1 user group 1.1M Jan 14 09:53 pippo
    drwxr-xr-x   5 user group  160 Jan 14 09:53 .
    -rw-r--r--   1 user group  514 Jan 14 06:42 pluto
    -rw-r--r--   1 user group 106M Jan 13 12:12 paperino
    drwx------+ 47 user group 1.5K Jan 12 18:08 ..
    ```

{% comment %}
ls -lath --color=auto
{% endcomment %}

1. Scrivi le funzioni bash `marco` e `polo` col seguente comportamento.
Quando invochi `marco` la cartella corrente dev'essere, in qualche modo,
salvata, e quando invochi `polo`, in una qualsiasi cartella, devi spostarti
nella carella salvata dalla funzione precedente.  
Per facilitare il debug puoi scrivere il codice in un file `marco.sh` e
ricaricare le definizioni delle funzioni nella shell con `source marco.sh`.

{% comment %}
marco() {
    export MARCO=$(pwd)
}

polo() {
    cd "$MARCO"
}
{% endcomment %}

1. Ipotizziamo di avere un comando che fallisce raramente. Per poter debuggarlo
   è necessario leggere il suo output e potrebbe richiedere molto tempo
   attendere per un fallimento. Scrivi uno script bash che esegua lo script
   seguente finché non fallisce, catturando i suoi output (STDOUT e STDERR) su
   dei file da stampare a video al termine. Punti bonus per chi stampa anche il
   numero di esecuzioni necessarie prima del fallimento.

    ```bash
    #!/usr/bin/env bash

    n=$(( RANDOM % 100 ))

    if [[ n -eq 42 ]]; then
       echo "Qualcosa è andato storto"
       >&2 echo "L'errore è stato usare numeri magici"
       exit 1
    fi

    echo "Tutto a posto"
    ```

{% comment %}
#!/usr/bin/env bash

count=0
until [[ "$?" -ne 0 ]];
do
  count=$((count+1))
  ./random.sh &> out.txt
done

echo "found error after $count runs"
cat out.txt
{% endcomment %}

1. Come abbiamo visto nella lezione l'opzione `-exex` di `find` può essere molto
   potente per effettuare operazioni su i file che si sta cercando. Tuttavia
   come fare se volgiamo fare un'azione con **tutti** i file, come ad esempio
   creare un archivio compresso? Come visto fin'ora i comandi accettano input
   sia da STDIN che tramite argomenti. Quando si concatenano comandi però stiamo
   connettendo l'STDOUT del primo all'STDIN del secondo: questo non ci aiuta a
   connettere il comando `tar` che accetta input solo dai suoi argomenti. Per
   risolvere il problema c'è il comando
   [`xargs`](https://www.man7.org/linux/man-pages/man1/xargs.1.html) che esegue
   un comando usando STDIN come argomenti. Ad esempio `ls | xargs rm`
   cancellerà i file nella cartella corrente.

   Il tuo compito è quello di scrivere un comando che ricorsivamente trova tutti
   i file HTML nella cartella e crea un archivio compresso che li contiene.
   Presta attenzione affinché lo script funzioni anche i nomi dei file
   contengono spazi (suggerimento: controlla a cosa serve il flag `-d` di
   `xargs`).
   {% comment %}
   `find . -type f -name "*.html" | xargs -d '\n'  tar -cvzf archive.tar.gz`
   {% endcomment %}

   Se lavori con macOS presta attenzione al fatto che la versione BSD di `find`
   è diversa da quella inclusa nelle
   [GNU coreutils](https://en.wikipedia.org/wiki/List_of_GNU_Core_Utilities_commands). 
   Puoi usare `-print0` su `find` e il flag `-0` su `xargs`. Come utente macOS
   devi ricordarti che le utility a linea di comando possono differire da quelle
   GNU presenti su GNU/Linux; puoi installare la versione GNU [usando
   brew](https://formulae.brew.sh/formula/coreutils).

1. (Avanzato) Scrivi un comando che trova ricorsivamente i file modificati più
   recentemente in una cartella. O più genericamente riesci ad elencare i file
   (ricorsivamente) per data?
