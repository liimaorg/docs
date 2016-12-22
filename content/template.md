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

## Zugriff auf Modell

### Globale Elemente

Globale Elemente die in jedem Template zur Verfügung stehen:

* appServer (current Applicationserver Resource)
* node (current Node Resource)
* runtime
* applications
* env (Environment)
* deployment
* deploymentId
* deploy (true wenn in deploymodus)

#### appServer

Zugriff auf den appServer der aktuellen Generierung erfolgt aus jedem Template über den globalen Zugriff ``appServer``

```
${appServer.propertyName}
oder
${appServer.propertyName.currentValue}
```

Nun kann via appServer mittels
```
${appServer.testapplication.name}

```
direkt auf die Applikation testapplikation zugegriffen werden

#### node

Zugriff auf den aktuellen Node der aktuellen Generierung erfolgt aus jedem Template über den globalen Zugriff ``node``

```
${node.propertyName}
oder
${node.propertyName.currentValue}
```

#### runtime

Zugriff auf die Runtime (bspw. JBoss EAP) des Applikationservers erfolgt aus jedem Template über den globalen Zugriff ``runtime``

```
${runtime.propertyName}
oder
${runtime.propertyName.currentValue}
```

#### applications

unter ``applications`` stehen alle Applikationen die generiert werden als Hash zur Verfügung

```
<#list applications?values as app>
  ${app.name}
</#list>
```

#### Umgebung (env)

unter ``env`` stehen einem die Informationen der aktuellen Umgebung zur Verfügung

```
${env.id}
${env.name}
${env.domain}
```
Als Resultat wird das folgende ausgegeben
```
5 # id
D #Environment
Dev #Domain
```

#### Deployment

unter ``deployment`` stehen einem die Informationen zum aktuellen Deployment zur Verfügung

```
${deployment.id} 
${deployment.trackingId} # Tracking ID if mutliple Deployments are done at the same time
${deployment.generationStateDate} # Date of generation
${deployment.requestUser} 
${deployment.jobCreationDate}
${deployment.confirmationUser}
${deployment.confirmationDate}
${deployment.amwLogFile} # path of logfile eg. /tmp/amw/logs/123_node1.log
${deployment.targetLogPrefix} # Prefix of the current log location eg. /tmp/amw/logs/-1_
${deployment.generationdir} # the current generationDir eg. /tmp/amw/gen/testapplicationserver/D_node1_2016-09-28-17-23-47.800_test

${deployment.generationModus} # Values TEST (Testgeneration), DEPLOY (Deployment), PREDEPLOY (Predeploy phase), SIMULATE (simulation)

## Release Information
${deployment.release.id}
${deployment.release.name}
${deployment.release.installationDate}

## Runtime analog ${runtime}
${deployment.runtime.id}
${deployment.runtime.name}

## NodeJob 
${deployment.nodejob.id}

```
 
##### DeploymentId und deploy true

für die beiden properties ``deloymentid`` und ``deploy`` um zu abfragen ob es sich um den Deploymodus handelt, gibt es die beiden Convenience-Methoden.

```
${deploymentId}
${deploy}

```


### Relations

Auf relatete Resroucen kann wie folgt zu gegriffen werden.

#### direkter Zugriff über Name

Das folgende Template, das auf einer applikation sitzt greift auf die Properties der angehängten Datenbank mit dem Namen **mysql** zu.

```
${mysql.name}

```

#### via consumedResTypes

consumedResTypes ist eine Gruppierung der consumed Relations über den Typ

```
${consumedResTypes.Database.mysql.name}

```


### Property

Der Zugriff auf ein Property erfolgt über die Angabe seines namens (propertyName), die Properties der aktuellen Resource befinden sich direkt unter root:

```
${propertyName}
oder
${propertyName.currentValue}
```

Will man beispielsweise auf ein ApplicationServer Property zugreifen, wird dies wie folgt gemacht:

```
${appServer.propertyName}
oder
${appServer.propertyName.currentValue}
```



#### Zugriff auf PropertyDescriptoren
Auf die Metainformationen einer Property kann mittels seines TechnicalKeys, gefolgt von ``_descriptor`` zugegriffen werden. Für ein Property mit dem TechnicalKey "propertyName" sähe dies im Template so aus:

```
${propertyName._descriptor.propertyName}
${propertyName._descriptor.displayName}
${propertyName._descriptor.encrypt}
${propertyName._descriptor.valueOptional}
${propertyName._descriptor.keyOptional}
${propertyName._descriptor.validationLogic}
${propertyName._descriptor.propertyComment}
${propertyName._descriptor.cardinalityProperty}
${propertyName._descriptor.technicalKey}
${propertyName._descriptor.defaultValue}
${propertyName._descriptor.exampleValue}
${propertyName._descriptor.machineInterpretationKey}
```

Sollte ein TechnicalKey einen Punkt "." enthalten, so ist dieser via Backslash ``\`` zu escapen:
```
${property\.Name._descriptor.propertyName}
```

Mittels der hasContent Methode auf einem Property kann abgefragt werden, ob ein Wert vorhanden ist oder nicht.
gibt true zurück wenn currentValue null oder [empty String] ist.
```
${propertyName.hasContent}
```

#### PropertyTags
Eine Liste der dem Property zugeordneten Tags, ist via 
```
${propertyName._descriptor.tags}
```
verfügbar. Mittels
```
${propertyName._descriptor.hasTag("tag")}
```
kann überprüft werden, ob dem Property ein bestimmter Tag zugeordnet ist.

#### Auflösen der PropertyWerte im Generator
Ein PropertyDescriptor kann mehrere PropertyValues besitzen - für einen bestimmten Kontext gibt es aber immer nur einen einzigen gültigen Wert. Die Auswertung der PropertyValues für einen bestimmten Kontext folgt einer spezifischen Priorisierungsreihenfolge: Für einen Kontext/eine Umgebung wird versucht, dieser aufgrund der Kontexthierarchie aufzulösen - also z.B. zuerst "B", wenn dort nichts definiert wurde auf "DEV" ? "GLOBAL". Dabei sollen Werte auf der Instanz wie auch auf deren Typen (inkl. Typhierarchie) geprüft. Dabei kommen die folgenden beiden Möglichkeiten erst dann zum Tragen wenn bei der Auflösung kein PropertyValue definiert wurde (sprich vom Benutzer kein Wert gesetzt wurde):

* Berücksichtigung synthetisierter Werte: Besitzt der Propertydescriptor ein MachineInterpretation-Key, so wird der Wert zum Auflösungszeitpunkt aufgelöst - der aufgelöste Wert wird nur für die aktuelle Generierung verwendet und wird nicht persistiert. (vgl. \ref{subsec:functonalProperties})
* Berücksichtigung eines Default-Values: Der allenfalls auf dem Propertydescriptor definierte Default-Wert übernommen.

#### Dynamische Properties

Der Wert von dynamischen Properties wird nicht von einem Benutzer definiert - stattdessen steht hinter einem dynamischen Property eine Auflösungslogik, welche den Wert herleitet. Ein gängiges Beispiel ist hier die Auflösung einer Webservice-Endpoint-URL zu erwähnen. AMW definiert dynamische Properties in Form eines MachineInterpretationKey (MIK).  Anhand dieses Keys wird in AMW definiert, welche Funktionalität verwendet wird um dieses dynamisch Property abzufüllen.
Ist ein dynamisches Property optional und erhält nach dessen Auflösung einen leeren Wert, so wird dieses nicht in das Template geschrieben. Der Wert des dynamischen Properties kann vom Benutzer überschrieben werden. Ist ein Wert vom Benutzer gesetzt, wird die Auflöselogik nicht ausgeführt.
Terminologisch können wir nun zwischen den dynamischen properties und den einfachen Properties unterscheiden. Die einfachen Properties entsprechen den bisher in AMW etablierten Properties.
Nun haben wir gesehen, was dynamische Properties sind, wie sie eingesetzt werden und wie sie sich von einfachen Properties unterscheiden. Für die Umsetzung ist von dynamischen Properties müssen deren Bestandteile analysiert werden. Konzeptionell besteht ein dynamisches Property aus zwei Teilen: 

* Enthält vom Benutzer definierte Logik, wie ein Propertywert basierend auf einfachen Properties und Funktionen aufgelöst wird.
* Das dynamische Property unterscheidet sich vom einfachen Property durch die Angabe des MIK im Propertydeskriptor. Selbst enthält das dynamische Property keine Funktionalität.

## Logoutput in AMW verfügbar machen.

Mit dem Feature aus https://github.com/a-gogo/agogo/pull/13 wird sämtlicher Output (Stdout, Stderr) der das AMW Run Script erzeugt gelesen und entprechend im GUI unter Logs pro Deployment zur Verfügung gestellt.

Man hat gleichzeitig die Möglichkeit im AMW Run Script mittels Outputumleiten in das Logfile zu schreiben

```
echo "logoutput" >> ${deployment.amwLogFile}
```
**Wichtig**: es muss beachtet werden, dass der Output der manuell ins Logfile geschrieben wird, vor dem Output des Scripts im Logfile erscheint.

### Eigenes Logfile
Sämtliche Logfiles die für ein bestimmtes Deployment (id: 1) werden durch AMW eingelesen, sie müssen lediglich dem folgenden Namensschema entsprechen:

``` 
${deployment.targetLogPrefix}customlogfile.log
# eg. /tmp/amw/logs/1_customlogfile.log
```
oder mit Nodenamen
``` 
${deployment.targetLogPrefix}_${node.name}_customlogfile.log
# eg. /tmp/amw/logs/1_node1_customlogfile.log
```

## Funktionen

### Globale Funktionen

AMW bietet die Möglichkeit unter Settings --> Global Functions globale Funktionen zur Verfügung zu stellen. Diese Funktionen können wiederum in den Templates verwendet / included werden

```
<#include globalfunctions >

${myglobalFunction()}

```

### Resource Funktionen

Auf Resource Ebene können Funktionen ebenso definiert werden, innerhalb der Templateausführung stehen die Funktionen dann auf der entsprechenden Resource zur Verfügung.
Diese Funktionen werden verwendet um in dynamischen Properties als MIKs entsprechend Werte aufzulösen.

Diese Funktionen können auch in einem Template aufgerufen werden, wenn man sich auf der selben Resource befindet, sieht das wie folgt aus:

```

${amwfunction.myFunction()}

```
