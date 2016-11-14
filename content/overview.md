# AMW Overview

Mit AMW können Sie sämtliche Konfigurationen Ihrer Applikationsumgebung auf beliebig vielen Umgebungen in unterschiedlichen Versionen verwalten und automatisiert deployen.

## Key Features

AMW unterstützt die Middleware Konfiguration und Automatisierung auf den folgenden Ebenen:

* Serveraufbau
* Applikationsverteilung
* Konfigurationsverteilung

### Strukturierte Verwaltung der Konfiguration

Das generische Modell ermöglicht die strukturierte Verwaltung der Konfiguration und reduziert mögliche Redundanzen in Ihrer Konfiguration. Dank dem hierarchischen Aufbau können Property Werte global, auf Umgebungsebene, pro Applikationsserver oder pro Applikation definiert respektive überschrieben werden.

### Abbilden von  Abhängigkeiten

Beziehungen zwischen Ressourcen (Applikationen, Datenbanken, Webservices) werden mit AMW abgebildet und können zusätzliche Konfigurationen und/oder Property-Werteanpassungen enthalten.

### Templatebasierte Generierung beliebiger Konfiguration

Die im generischen Modell abgebildeten Properties und deren Werte können mittels Runtime-spezifischen Templates in die von der Applikation und dem Applikationsserver benötigte Struktur generiert werden.

### Versionierung  und Historisierung

Sämtliche Änderungen an der Konfiguration werden protokolliert und abgespeichert. Zusätzlich können explizite Konfigurationsstände übergreifend getagged und somit einmal erreichte Zustände gesichert werden.


### Automatisiertes Deployment von Applikation und Konfiguration

Deployen von Applikationen und ganzen Umgebungen zeitgesteuert, per Knopfdruck oder via REST API integriert in Ihre Workflows und Deployment Pipelines.
 
Das ausgelagerte Deploymentmodul ermöglicht das Anbinden von diversen Applikationsrepositories, aus denen die Deployables skriptgesteuert geladen respektive gebuildet werden können:

* Maven Repository
* RPM Repository
* Generierung RPM on the fly
* Filebasiert
* etc.

### Environment  Health Check

Mit dem Environment Health Check kann nach einem Deployment einer Applikation oder einer gesamten Umgebung deren Status und Funktionalität überprüft werden. So können auf Applikationsebene sogenannte Shakedowntests definiert werden, welche zum Überprüfen der Applikation und deren näheren Umgebung ausgeführt werden.

### Abbildung von  Entwicklungsprozessen

Über das flexible Rollen- und Berechtigungskonzept können Sie die beteiligten Stellen im Entwicklungsprozess dynamisch abbilden.
So wird beispielsweise der Software Ingenieur berechtigt, Konfigurationen für seine Applikation auf den definierten Entwicklungsumgebungen eigenständig zu verwalten und/oder Deployments auf anderen Umgebungen anzufordern, welche nach einer weiteren Bestätigung ausgeführt werden.
