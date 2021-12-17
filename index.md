---
layout: page
title: Il semestre mancante del tuo corso di studi in informatica
---

I corsi insegnano tutti gli argomenti avanzati dell'informatica, dai sistemi
operativi al machine learning, ma c'è un argomento importante che raramente
viene trattato ed è solitamente lasciato agli studenti: l'abilità nell'uso
degli strumenti. In questo corso vedremo come diventare esperti della linea di
comando, usare un editor di testo potente, utilizzare le comode funzionalità
dei sistemi per il controllo di versione, e molto altro!

Gli studenti impiegano centinaia di ore ad usare questi strumenti durante i
corsi curriculari (e migliaia durante la loro carriera), quindi ha senso rendere
l'esperienza più fluida possibile. Padroneggiare questi strumenti non solo ti
permette di impiegare meno tempo per capire come usarli per i tuoi scopi, ma ti
aiuta anche a risolvere problemi che prima sarebbero sembrati impossibili.

Leggi le [motivazioni di questo corso](/about/).

# Avanzamento della traduzione

- [X] index
- [X] about
- [ ] 2019
    - [ ] index
    - [ ] course-overview
    - [ ] virtual-machines
    - [ ] shell
    - [ ] command-line
    - [ ] data-wrangling
    - [ ] editors
    - [ ] version-control
    - [ ] dotfiles
    - [ ] backups
    - [ ] automation
    - [ ] machine-introspection
    - [ ] program introspection
    - [ ] package-management
    - [ ] os-customization
    - [ ] remote-machines
    - [ ] web 
    - [ ] security
- [ ] 2020
    - [X] course-shell
    - [ ] sehll-tools
    - [ ] editors
    - [ ] data-wrangling
    - [ ] command-line
    - [ ] version-control
    - [ ] debugging-profiling
    - [ ] metaprogramming
    - [ ] security
    - [ ] potpurri
    - [ ] qa

# Ultime lezioni

<ul>
{% assign lectures = site['2020'] | sort: 'date' %}
{% for lecture in lectures %}
    {% if lecture.phony != true %}
        <li>
        <strong>{{ lecture.date | date: '%d/%m/%y' }}</strong>:
        {% if lecture.ready %}
            <a href="{{ lecture.url }}">{{ lecture.title }}</a>
        {% else %}
            {{ lecture.title }} {% if lecture.noclass %}[no class]{% endif %}
        {% endif %}
        </li>
    {% endif %}
{% endfor %}
</ul>

Le videoregistrazioni delle lezioni sono disponibili [su
YouTube](https://www.youtube.com/playlist?list=PLyzOVJj3bHQuloKGG59rS43e29ro7I57J).

# Informazioni sul corso

**Docenti**: Questo corso è tenuto da [Anish](https://www.anishathalye.com/), [Jon](https://thesquareplanet.com/), e [Jose](http://josejg.com/).  
**Domande**: Manda una mail (in inglese) a [missing-semester@mit.edu](mailto:missing-semester@mit.edu).

# Al di fuori del MIT

Abbiamo condiviso questo corso al di fuori del MIT nella speranza possa essere
utile anche ad altri. Puoi trovare post e discussioni su:

 - [Hacker News](https://news.ycombinator.com/item?id=22226380)
 - [Lobsters](https://lobste.rs/s/ti1k98/missing_semester_your_cs_education_mit)
 - [/r/learnprogramming](https://www.reddit.com/r/learnprogramming/comments/eyagda/the_missing_semester_of_your_cs_education_mit/)
 - [/r/programming](https://www.reddit.com/r/programming/comments/eyagcd/the_missing_semester_of_your_cs_education_mit/)
 - [Twitter](https://twitter.com/jonhoo/status/1224383452591509507)
 - [YouTube](https://www.youtube.com/playlist?list=PLyzOVJj3bHQuloKGG59rS43e29ro7I57J)

# Traduzioni

- [Originale in inglese](https://missing.csail.mit.edu/)

- [Cinese (Semplificato)](https://missing-semester-cn.github.io/)
- [Cinese (Tradizionale)](https://missing-semester-zh-hant.github.io/)
- [Giapponese](https://missing-semester-jp.github.io/)
- [Coreano](https://missing-semester-kr.github.io/)
- [Portoghese](https://missing-semester-pt.github.io/)
- [Russo](https://missing-semester-rus.github.io/)
- [Serbo](https://netboxify.com/missing-semester/)
- [Spagnolo](https://missing-semester-esp.github.io/)
- [Turco](https://missing-semester-tr.github.io/)
- [Vietnamita](https://missing-semester-vn.github.io/)

Attenzione: questi ultimi sono link esterni alle traduzioni della community, che
non sono state verificate dagli autori originali.

Hai creato una traduzione delle note del corso? Invia una
[pull request](https://github.com/missing-semester/missing-semester/pulls) in
modo che vengano aggiunte alla lista!

## Ringraziamenti

Un dovuto ringraziamento a Elaine Mello, Jim Cain, and [MIT Open
Learning](https://openlearning.mit.edu/) per aver reso possibile la
registrazione delle lezioni; Anthony Zolnik and [MIT AeroAstro](https://aeroastro.mit.edu/)
per la strumentazione A/V; Brandi Adams e [MIT EECS](https://www.eecs.mit.edu/)
per aver supportato questo corso.

---

<div class="small center">
<p><a href="https://github.com/missing-semester-it/missing-semester-it.github.io">Codice sorgente</a>.</p>
<p>Rilasciato sotto licenza CC BY-NC-SA.</p>
<p>Guarda <a href="/license/">qui</a> per le linee guida sui contributi e traduzioni.</p>
</div>
