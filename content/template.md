# Template

In diesem Kapitel wird beschrieben wie innerhalb der Templates auf das Datenmodell im AMW zugegriffen werden kann, welche Funktionen zur Verfügung stehen und wie die entsprechenden Werte ausgegeben werden können.

## Template Sprache

AMW verwendet [Freemarker](http://freemarker.org/) als Templating Sprache. Freemarker ist sehr mächtig, so haben Sie die Möglichkeit innerhalb der Templates in AMW, die die Konfigurationsfiles aufbereiten 
Logik einzubauen.

Grundsätzlich stehen folgende Funktionen zur Verfügung:

* Werte aus dem Modell ausgeben ``${key}``
* If Else Conditions / Vergleiche
* Formatieren
* Funktionen / Macros definieren und ausführen
* Listen, Hashes
* Operationen auf Werten String, Numbers, ...
* ...

Eine komplette Liste der Funktionalität steht unter http://freemarker.org/ zur Verfügung.

## Modell


## Funktionen

### Globale Funktionen

AMW bietet die Möglichkeit unter Settings --> Global Functions globale Funktionen zur Verfügung zu stellen. Diese Funktionen können wiederum in den Templates verwendet / included werden

```
<#include globalfunctions >

${myglobalFunction()}

```

### Resource Funktionen

Auf Resource Ebene können Funktionen ebenso definiert werden, innerhalb der Templateausführung stehen die Funktionen dann auf der entsprechenden Resource zur Verfügung.