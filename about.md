---
layout: lecture
title: "Perché è nato questo corso?"
---

Studiando informatica si seguono molti corsi che si addentrano negli aspetti
avanzati della materia. Si affrontano argomenti complessi come sistemi
operativi, linguaggi di programmazione, fino al machine learning. Ma una cosa
manca da quasi tutti i corsi di studio in informatica, e viene lasciata agli
studenti: un'alfabetizzazione all'uso degli strumenti e delle _utilities_.

Negli anni abbiamo contribuito all'insegnamento di molti corsi al MIT, e abbiamo
notato che molti studenti hanno una conoscenza limitata degli strumenti a loro
disposizione. I computer sono nati per automatizzare il lavoro manuale, eppure
gli studenti spesso eseguono azioni ripetitive a mano, oppure non riescono ad
ottenere il massimo dagli strumenti quali sistemi per il controllo di versione
e editor di testo. Nel miglior dei casi, questi tentativi risultano in una
perdita di tempo; nei casi peggiori possono comportare anche perdita di dati o
impossibilità a concludere certe attività.

Tutto questo non fa parte dei curriculum universitari: agli studenti non viene
mai mostrato come usare questi strumenti, o almeno non come usarli in maniera
efficace, e quindi come non perdere giorni in un'attività che dovrebbe essere
semplice. Il piano di studi classico dei corsi in informatica manca dunque di
nozioni fondamentali sull'ecosistema che sta attorno alla programmazione e che
può rendere la vita agli studenti significativamente più semplice.

# Il semestre mancante del tuo corso di studi in informatica

Per porre rimedio a questo, abbiamo avviato un corso che tratti tutti gli
aspetti da noi considerati cruciali per essere un efficiente informatico. Il
corso è molto pratico e prevede una dimostrazione introduttiva agli strumenti e
alle tecniche che potrai immediatamente applicare in una vasta gamma di
situazioni che incontrerai. Il corso si svolge durante la sessione delle
attività indipendenti del MIT a Gennaio 2020 - un semestre di un mese durante il
quale si svolgono brevi lezioni tenute dagli studenti. Mentre le lezioni in
presenza sono riservate agli studenti del MIT, metteremo pubblicamente a
disposizione tutti i materiali, insieme alle registrazioni.

Se questo ti ispira potrebbe fare al caso tuo. Ecco alcuni esempi concreti di
quello che andremo a vedere:

## Shell dei comandi

Come automatizzare azioni comuni e ripetitive con gli _alias_, _script_ e
sistemi di _build_. Niente più copia e incolla di comandi da un file di testo.
Niente più "esegui questi 15 comandi uno dopo l'altro". Niente più "hai
dimenticato di eseguire questo passaggio" o "hai dimenticato quest'argomento".

Ad esempio, la ricerca nella cronologia dei comandi può facilmente farti
risparmiare molto tempo. Nell'esempio  qua sotto mostriamo alcuni trucchetti per
navigare nella cronologia della shell alla ricerca del comando `convert`.

<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/history.mp4" type="video/mp4">
</video>

## Sistemi per il controllo di versione

Come usare _correttamente_ i sistemi per il controllo di versione, e sfruttarli
per salvarti dai disastri, collaborare con gli altri, e trovare ed isolare
facilmente i cambiamenti problematici. Niente più `rm -rf; git clone`, niente
più conflitti di _merge_ (o meglio, meno conflitti), niente più grosse porzioni
di codice commentate, niente più affannose ricerche a che modifica ha rotto il
tuo codice, niente più "oh no, hai cancellato il codice che funzionava?!". Ti
insegneremo anche come contribuire ai progetti degli altri con le _pull
requests_!

Nell'esempio sottostante usiamo `git bisect` alla ricerca del _commit_ che ha
rotto un _unit test_ e poi lo aggiustiamo con `git revert`.
<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/git.mp4" type="video/mp4">
</video>

## Editor di testo

Come modificare in maniera efficiente file di testo da linea di comando, sia in
locale che in remoto, e sfruttare le _feature_ avanzate degli editor. Niente più
copia di file e modifiche ripetitive.

Le macro di vim sono una delle sue migliori funzionalità, in questo esempio
convertiamo velocemente una tabella html in csv usando una macro di vim.
<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/vim.mp4" type="video/mp4">
</video>

## Macchine remote

Come lavorare tranquillamente con macchine remote usando le chiavi SSH e il
_multiplexing_ del terminale. Niente più terminali bloccati per un solo comando
in esecuzione, niente più dover inserire la password ad ogni connessione, niente
più dover perdere la sessione di lavoro perché cade la connessione o bisogna
riavviare il portatile.

Nell'esempio seguente usiamo `tmux` per tenere attiva la sessione sul server
remoto e `mosh` per supportare la disconnessione della rete.

<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/ssh.mp4" type="video/mp4">
</video>

## Trovare file

Come trovare velocemente i file che stai cercando. Niente più ricerche disperate
nelle cartelle fino a che non trovi il pezzo di codice desiderato.

In questo esempio cerchiamo file con `fd` e pezzi di codice con `rg`. Inoltre ci
spostiamo di cartella ed apriamo file in posizioni usate di frequente con
`fasd`.

<video autoplay="autoplay" loop="loop" controls muted playsinline  oncontextmenu="return false;"  preload="auto"  class="demo">
  <source src="/static/media/demos/find.mp4" type="video/mp4">
</video>

## Manipolazione dei dati

Come modificare, visualizzare, leggere, plottare e calcolare facilmente a
partire da dati grezzi, direttamenti dalla linea di comando. Niente più copia e
incolla dai file di log, o calcoli statistici manuali, come niente più grafici
da fogli di calcolo.

## Macchine virtuali

Come usare le macchine virtuali per provare nuovi sistemi operativi, isolare
progetti diversi, e tenere la tua macchina pulita. Niente più corruzioni
accidentali del tuo computer, niente più milioni di pacchetti installati con
versioni diverse.

## Sicurezza

Come usare internet senza rivelare i tuoi segreti al mondo. Niente più password
banali, niente più WiFi pubblicamente aperti, niente più messaggi in chiaro.

# Conclusioni

Questo, e molto altro, sarà trattato nelle 12 lezioni, ognuna delle quali
include degli esercizi per familiarizzare meglio con gli strumenti. Se non puoi
aspettare Gennaio, puoi dare un occhio alle lezioni di [Hacker
Tools](/2019), tenute da noi durante lo IAP
l'anno scorso: è il precursore di questo corso e tratta quasi gli stessi
argomenti.

Speriamo di vedervi a gennaio, online o in presenza!

Buon hacking,<br>
Anish, Jose, and Jon
