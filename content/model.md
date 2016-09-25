# AMW Datenmodell

Grundsätzlich besteht das Modell in dem Sie die Konfiguration in AMW abbilden aus den folgenden Grundelementen:

* ApplicationServer (AS) - Der Applikationsserver auf dem die Applikationen deployed werden
* Application (App) - Die zu deployende Applikation
* Node - Der Node in Kombination mit einem AS entspricht einem Deployment des AS auf einem Server
* Runtime - Die Runtime definiert am AS um welchen Applikationsserver es sich handelt, typischerweise JBoss EAP 6, JBoss EAP 7, Websphere, ...
* Resources - Als Resourcen werden alle anderen Elemente die an einem AS, einer App, einer Node oder Runtime als Abhängigkeiten angehängt werden können
 * Datenbank an Applikation
 * Webservice SOAP / REST
 * Queue
 * Zertifikate
 * ...
 
 
Allegmein sprechen wir bei den obigen Elementen von **Ressourcen** die zu einander mittels Relations in Beziehung stehen. 

Jede Ressource besitzt einen **Namen** und einen **ResourceType**

## ResourceTypes

ResourceTypes entsprechen der generalisierten Ausprägung von Ressourcen. So werden beispielsweise auf dem RessourceType ApplikationServer generelle Informationen, Templates, Properties usw. eingepflegt, die sonst auf Instanzebene redundat pro Applicationserver verwaltet werden müssten.

Man hat also damit die Möglichkeit, generell geltende Sachen für alle Applikationsserver einmal zu definieren.

Ein ResourceInstanz ist immer einerseits über den ResourceType und über die effektive Instanz definiert.

Auf ResourceTypes können die folgenden Elemente definiert werden:

* PropertyDescriptoren (PropertyKeys)
* PropertyWerte
* Templates
* Functions

Auf ResourceInstance Ebene können folgende Elemente definiert werden:

* Zusätzliche PropertyDescriptoren (Zusätzlich zu den Descriptoren der ResourceTypes)
* PropertyWerte
* PropertyWerte von ResourceTypes können überschrieben werden.
* Templates
* Functions
* Relations

## PropertyTypes

Ein PropertyType (PropertyTypeEntity) ist als Template für PropertyDescriptors zu verstehen.
Folgende Werte können auf dem PropertyType definiert werden:

* id: eindeutiger Identifier
* propertyTypeName: Name des PropertyType
* validationRegex: Validierungslogik für den Property-Wert
* encrypt: gibt an ob der Property-Wert verschlüsselt wird
* propertyTags: Einsatzzwecke, eine Sammlung von Gruppierungselementen, welche in den Templates als Selektionskriterium zur Verfügung steht

## Properties

In AMW wird ein Property (PropertyEntity) durch eine Kombination aus sogenannten PropertyDescriptors und Property-
Values repräsentiert: Während ein PropertyDescriptor die eigentlichen Eigenschaften eines Properties enthält, stellen die PropertyValues kontextabhängige Werte eines Properties dar.
Folgende Werte können auf dem PropertyDescriptor definiert werden:

* id: eindeutiger Identifier
* technicalKey: TechnicalKey - vollständiger, technischer Schlüssel inkl. komplexem Namespace zur Sicherstellung der Uniqueness
* displayName: Optionaler Name für die Darstellung im GUI, da der propertyName in gewissen Fällen sehr lang und unleserlich sein kann
* encrypt: gibt an ob der Property-Wert verschlüsselt wird
* valueOptional: gibt an ob der Property-Wert null sein darf
* keyOptional: gibt an, ob ein Property mit diesem Key im Generator zur Verfügung gestellt werden muss
* testing: gibt an ob das Property für Shakedowntests verwendet wird, diese Properties werden im GUI nur im Testingmode angezeigt.
* validationLogic: Validierungslogik für den Property-Wert
* propertyComment: Kommentar zu diesem Property
* cardinalityProperty: wenn dieser Wert nicht null ist, handelt es sich um ein SystemProperty und hat eine spezielle Bedeutung. Ein solche Property kann z.Bsp. nicht von einem Benutzer gelöscht werden. Ausserdem wirkt sich die Kardinalität auf die Darstellungsreihenfolge im GUI aus.
* propertyTypeEntity: das Template für das Property, gewisse Werte werden vom Typ übernommen falls sie nicht im descriptor überschrieben werden. Falls null handelt es sich um ein Custom-Property.
* defaultValue: Defaultwert falls auf dem Property kein value gesetzt ist.
* exampleValue: Beispielwert als Hilfe für den Benutzer
* machineInterpretationKey:
* propertyTags: Einsatzzwecke, eine Sammlung von Gruppierungselementen, welche in den Templates als Selektionskriterium zur Verfügung steht


## Beziehungen / Relations

Ressourcen können über sogenannte Relations in Beziehung zu einander gesetzt werden:

* App --> Datenbank, eine Applikation konsumiert eine Datenbank über eine ConsumedRelation
* App --> Webservice, eine Applikation stellt einen Webservice über eine ProvidedRelation zur Verfügung.


## Beispiel Applikation AMW

Als Beispiel hier beschreiben wir die Applikation AMW selber, AMW wird auf einem JBoss EAP 6.4 deployed und besitzt eine Datenbank und versendet via Mail Session Emails

In unserem Beispiel wird nur eine Applikation auf den Applicationserver deployed.

* Applicationserver: amw
* Application: amwApp
* Runtime: EAP64
* Database: oracle
* Node: Node1