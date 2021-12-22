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
[qui](https://github.com/koalaman/shellcheck).

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

# Shell Tools

## Finding how to use commands

At this point, you might be wondering how to find the flags for the commands in the aliasing section such as `ls -l`, `mv -i` and `mkdir -p`.
More generally, given a command, how do you go about finding out what it does and its different options?
You could always start googling, but since UNIX predates StackOverflow, there are built-in ways of getting this information.

As we saw in the shell lecture, the first-order approach is to call said command with the `-h` or `--help` flags. A more detailed approach is to use the `man` command.
Short for manual, [`man`](https://www.man7.org/linux/man-pages/man1/man.1.html) provides a manual page (called manpage) for a command you specify.
For example, `man rm` will output the behavior of the `rm` command along with the flags that it takes, including the `-i` flag we showed earlier.
In fact, what I have been linking so far for every command is the online version of the Linux manpages for the commands.
Even non-native commands that you install will have manpage entries if the developer wrote them and included them as part of the installation process.
For interactive tools such as the ones based on ncurses, help for the commands can often be accessed within the program using the `:help` command or typing `?`.

Sometimes manpages can provide overly detailed descriptions of the commands, making it hard to decipher what flags/syntax to use for common use cases.
[TLDR pages](https://tldr.sh/) are a nifty complementary solution that focuses on giving example use cases of a command so you can quickly figure out which options to use.
For instance, I find myself referring back to the tldr pages for [`tar`](https://tldr.ostera.io/tar) and [`ffmpeg`](https://tldr.ostera.io/ffmpeg) way more often than the manpages.


## Finding files

One of the most common repetitive tasks that every programmer faces is finding files or directories.
All UNIX-like systems come packaged with [`find`](https://www.man7.org/linux/man-pages/man1/find.1.html), a great shell tool to find files. `find` will recursively search for files matching some criteria. Some examples:

```bash
# Find all directories named src
find . -name src -type d
# Find all python files that have a folder named test in their path
find . -path '*/test/*.py' -type f
# Find all files modified in the last day
find . -mtime -1
# Find all zip files with size in range 500k to 10M
find . -size +500k -size -10M -name '*.tar.gz'
```
Beyond listing files, find can also perform actions over files that match your query.
This property can be incredibly helpful to simplify what could be fairly monotonous tasks.
```bash
# Delete all files with .tmp extension
find . -name '*.tmp' -exec rm {} \;
# Find all PNG files and convert them to JPG
find . -name '*.png' -exec convert {} {}.jpg \;
```

Despite `find`'s ubiquitousness, its syntax can sometimes be tricky to remember.
For instance, to simply find files that match some pattern `PATTERN` you have to execute `find -name '*PATTERN*'` (or `-iname` if you want the pattern matching to be case insensitive).
You could start building aliases for those scenarios, but part of the shell philosophy is that it is good to explore alternatives.
Remember, one of the best properties of the shell is that you are just calling programs, so you can find (or even write yourself) replacements for some.
For instance, [`fd`](https://github.com/sharkdp/fd) is a simple, fast, and user-friendly alternative to `find`.
It offers some nice defaults like colorized output, default regex matching, and Unicode support. It also has, in my opinion, a more intuitive syntax.
For example, the syntax to find a pattern `PATTERN` is `fd PATTERN`.

Most would agree that `find` and `fd` are good, but some of you might be wondering about the efficiency of looking for files every time versus compiling some sort of index or database for quickly searching.
That is what [`locate`](https://www.man7.org/linux/man-pages/man1/locate.1.html) is for.
`locate` uses a database that is updated using [`updatedb`](https://www.man7.org/linux/man-pages/man1/updatedb.1.html).
In most systems, `updatedb` is updated daily via [`cron`](https://www.man7.org/linux/man-pages/man8/cron.8.html).
Therefore one trade-off between the two is speed vs freshness.
Moreover `find` and similar tools can also find files using attributes such as file size, modification time, or file permissions, while `locate` just uses the file name.
A more in-depth comparison can be found [here](https://unix.stackexchange.com/questions/60205/locate-vs-find-usage-pros-and-cons-of-each-other).

## Finding code

Finding files by name is useful, but quite often you want to search based on file *content*. 
A common scenario is wanting to search for all files that contain some pattern, along with where in those files said pattern occurs.
To achieve this, most UNIX-like systems provide [`grep`](https://www.man7.org/linux/man-pages/man1/grep.1.html), a generic tool for matching patterns from the input text.
`grep` is an incredibly valuable shell tool that we will cover in greater detail during the data wrangling lecture.

For now, know that `grep` has many flags that make it a very versatile tool.
Some I frequently use are `-C` for getting **C**ontext around the matching line and `-v` for in**v**erting the match, i.e. print all lines that do **not** match the pattern. For example, `grep -C 5` will print 5 lines before and after the match.
When it comes to quickly searching through many files, you want to use `-R` since it will **R**ecursively go into directories and look for files for the matching string.

But `grep -R` can be improved in many ways, such as ignoring `.git` folders, using multi CPU support, &c.
Many `grep` alternatives have been developed, including [ack](https://beyondgrep.com/), [ag](https://github.com/ggreer/the_silver_searcher) and [rg](https://github.com/BurntSushi/ripgrep).
All of them are fantastic and pretty much provide the same functionality.
For now I am sticking with ripgrep (`rg`), given how fast and intuitive it is. Some examples:
```bash
# Find all python files where I used the requests library
rg -t py 'import requests'
# Find all files (including hidden files) without a shebang line
rg -u --files-without-match "^#!"
# Find all matches of foo and print the following 5 lines
rg foo -A 5
# Print statistics of matches (# of matched lines and files )
rg --stats PATTERN
```

Note that as with `find`/`fd`, it is important that you know that these problems can be quickly solved using one of these tools, while the specific tools you use are not as important.

## Finding shell commands

So far we have seen how to find files and code, but as you start spending more time in the shell, you may want to find specific commands you typed at some point.
The first thing to know is that typing the up arrow will give you back your last command, and if you keep pressing it you will slowly go through your shell history.

The `history` command will let you access your shell history programmatically.
It will print your shell history to the standard output.
If we want to search there we can pipe that output to `grep` and search for patterns.
`history | grep find` will print commands that contain the substring "find".

In most shells, you can make use of `Ctrl+R` to perform backwards search through your history.
After pressing `Ctrl+R`, you can type a substring you want to match for commands in your history.
As you keep pressing it, you will cycle through the matches in your history.
This can also be enabled with the UP/DOWN arrows in [zsh](https://github.com/zsh-users/zsh-history-substring-search).
A nice addition on top of `Ctrl+R` comes with using [fzf](https://github.com/junegunn/fzf/wiki/Configuring-shell-key-bindings#ctrl-r) bindings.
`fzf` is a general-purpose fuzzy finder that can be used with many commands.
Here it is used to fuzzily match through your history and present results in a convenient and visually pleasing manner.

Another cool history-related trick I really enjoy is **history-based autosuggestions**.
First introduced by the [fish](https://fishshell.com/) shell, this feature dynamically autocompletes your current shell command with the most recent command that you typed that shares a common prefix with it.
It can be enabled in [zsh](https://github.com/zsh-users/zsh-autosuggestions) and it is a great quality of life trick for your shell.

You can modify your shell's history behavior, like preventing commands with a leading space from being included. This comes in handy when you are typing commands with passwords or other bits of sensitive information.
To do this, add `HISTCONTROL=ignorespace` to your `.bashrc` or `setopt HIST_IGNORE_SPACE` to your `.zshrc`.
If you make the mistake of not adding the leading space, you can always manually remove the entry by editing your `.bash_history` or `.zhistory`.

## Directory Navigation

So far, we have assumed that you are already where you need to be to perform these actions. But how do you go about quickly navigating directories?
There are many simple ways that you could do this, such as writing shell aliases or creating symlinks with [ln -s](https://www.man7.org/linux/man-pages/man1/ln.1.html), but the truth is that developers have figured out quite clever and sophisticated solutions by now.

As with the theme of this course, you often want to optimize for the common case.
Finding frequent and/or recent files and directories can be done through tools like [`fasd`](https://github.com/clvv/fasd) and [`autojump`](https://github.com/wting/autojump).
Fasd ranks files and directories by [_frecency_](https://web.archive.org/web/20210421120120/https://developer.mozilla.org/en-US/docs/Mozilla/Tech/Places/Frecency_algorithm), that is, by both _frequency_ and _recency_.
By default, `fasd` adds a `z` command that you can use to quickly `cd` using a substring of a _frecent_ directory. For example, if you often go to `/home/user/files/cool_project` you can simply use `z cool` to jump there. Using autojump, this same change of directory could be accomplished using `j cool`.

More complex tools exist to quickly get an overview of a directory structure: [`tree`](https://linux.die.net/man/1/tree), [`broot`](https://github.com/Canop/broot) or even full fledged file managers like [`nnn`](https://github.com/jarun/nnn) or [`ranger`](https://github.com/ranger/ranger).

# Exercises

1. Read [`man ls`](https://www.man7.org/linux/man-pages/man1/ls.1.html) and write an `ls` command that lists files in the following manner

    - Includes all files, including hidden files
    - Sizes are listed in human readable format (e.g. 454M instead of 454279954)
    - Files are ordered by recency
    - Output is colorized

    A sample output would look like this

    ```
    -rw-r--r--   1 user group 1.1M Jan 14 09:53 baz
    drwxr-xr-x   5 user group  160 Jan 14 09:53 .
    -rw-r--r--   1 user group  514 Jan 14 06:42 bar
    -rw-r--r--   1 user group 106M Jan 13 12:12 foo
    drwx------+ 47 user group 1.5K Jan 12 18:08 ..
    ```

{% comment %}
ls -lath --color=auto
{% endcomment %}

1. Write bash functions  `marco` and `polo` that do the following.
Whenever you execute `marco` the current working directory should be saved in some manner, then when you execute `polo`, no matter what directory you are in, `polo` should `cd` you back to the directory where you executed `marco`.
For ease of debugging you can write the code in a file `marco.sh` and (re)load the definitions to your shell by executing `source marco.sh`.

{% comment %}
marco() {
    export MARCO=$(pwd)
}

polo() {
    cd "$MARCO"
}
{% endcomment %}

1. Say you have a command that fails rarely. In order to debug it you need to capture its output but it can be time consuming to get a failure run.
Write a bash script that runs the following script until it fails and captures its standard output and error streams to files and prints everything at the end.
Bonus points if you can also report how many runs it took for the script to fail.

    ```bash
    #!/usr/bin/env bash

    n=$(( RANDOM % 100 ))

    if [[ n -eq 42 ]]; then
       echo "Something went wrong"
       >&2 echo "The error was using magic numbers"
       exit 1
    fi

    echo "Everything went according to plan"
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

1. As we covered in the lecture `find`'s `-exec` can be very powerful for performing operations over the files we are searching for.
However, what if we want to do something with **all** the files, like creating a zip file?
As you have seen so far commands will take input from both arguments and STDIN.
When piping commands, we are connecting STDOUT to STDIN, but some commands like `tar` take inputs from arguments.
To bridge this disconnect there's the [`xargs`](https://www.man7.org/linux/man-pages/man1/xargs.1.html) command which will execute a command using STDIN as arguments.
For example `ls | xargs rm` will delete the files in the current directory.

    Your task is to write a command that recursively finds all HTML files in the folder and makes a zip with them. Note that your command should work even if the files have spaces (hint: check `-d` flag for `xargs`).
    {% comment %}
    find . -type f -name "*.html" | xargs -d '\n'  tar -cvzf archive.tar.gz
    {% endcomment %}

    If you're on macOS, note that the default BSD `find` is different from the one included in [GNU coreutils](https://en.wikipedia.org/wiki/List_of_GNU_Core_Utilities_commands). You can use `-print0` on `find` and the `-0` flag on `xargs`. As a macOS user, you should be aware that command-line utilities shipped with macOS may differ from the GNU counterparts; you can install the GNU versions if you like by [using brew](https://formulae.brew.sh/formula/coreutils).

1. (Advanced) Write a command or script to recursively find the most recently modified file in a directory. More generally, can you list all files by recency?
