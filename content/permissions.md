# Berechtigungen


|Berechtigung		|Action		|Beschrieb										|
|-----------------------|---------------|---------------------------------------------------------------------------------------|
|DEPLOYMENT		|ALL		|Darf deployen, die Action hat keinen Einfluss. Wer darf Request machen?						|
|RESOURCE_RELEASE_COPY_FROM_RESOURCE	|UPDATE		|Darf die Eigenschaften von einer Resource oder eines Predecessor kopieren		|
|   	|   | Es wird zusätzlich eine RESOURCE READ Berechtigung auf der Source-Resource benötigt		|
|RELEASE (settings)		|CREATE		|Darf erstellen							|
|			|READ		|Darf betrachten									|
|			|UPDATE		|Darf editieren										|
|			|DELETE		|Darf löschen										|
|SHAKEDOWNTEST		|CREATE		|Darf erstellen										|
|			|UPDATE		|Darf editieren										|
|			|DELETE		|Darf löschen										|
|RESOURCE		|READ		|Darf Resourcen betrachten								|
| 	  |UPDATE		|Darf Resourcen, deren Relations, Properties und Templates editieren			|
|			|		|Damit Consumed/Provided Relations hinzugefügt werden können, wird zusätzlich eine RESOURCE READ Berechtigung für die Slave-Resource benötigt	|
|			|		|Um Functions einer Resource zu betrachten/erstellen/editieren/löschen wird eine entsprechende RESOURCE_AMWFUNCTION Berechtigung benötigt	|
|			|		|Um Passwörter im Klartext zu sehen, wird eine RESOURCE_PROPERTY_DECRYPT Berechtigung benötigt	|
|RESOURCETYPE		|READ		|Darf ResourceTypen betrachten								|
|			|UPDATE		|Darf ResourceTypen, deren Relations, Properties und Templates editieren		|
|			|		|Damit Type Relations hinzugefügt werden können, wird zusätzlich eine RESOURCETYPE READ Berechtigung für die Slave-Resource benötigt	|
|			|		|Um Functions eines ResourceType zu betrachten/erstellen/editieren/löschen wird eine entsprechende RESOURCETYPE_AMWFUNCTION Berechtigung benötigt	|
|			|		|Um Passwörter im Klartext zu sehen, wird eine RESOURCETYPE_PROPERTY_DECRYPT Berechtigung benötigt	|
|RESOURCE_TEMPLATE	|READ		|Darf Resource Templates betrachten							|
|			|UPDATE		|Darf Resource Templates editieren							|
|			|DELETE		|Darf Resource Templates löschen							|
|RESOURCETYPE_TEMPLATE	|READ		|Darf ResourceTyp Templates betrachten							|
|			|UPDATE		|Darf ResourceTyp Templates editieren							|
|			|DELETE		|Darf ResourceTyp Templates löschen							|
|RESOURCE_AMWFUNCTION		|CREATE		|Darf Functions für Resourcen erstellen								|
|			|READ		|Darf Functions betrachten								|
|			|UPDATE		|Darf Functions bearbeiten								|
|			|DELETE		|Darf Functions löschen									|
|RESOURCETYPE_AMWFUNCTION		|CREATE		|Darf Functions für ResourceTypen erstellen								|
|			|READ		|Darf Functions betrachten								|
|			|UPDATE		|Darf Functions bearbeiten								|
|			|DELETE		|Darf Functions löschen									|

## Default Rollen
In Liima sind einige Default Rollen definiert. Diese sind so aufgebaut, dass sie miteinander kombiniert werden müssen um eine komplette Rolle zu ergeben, z.B. ein Benutzer braucht folgende Rollen um alle Konfigurations-Rechte in Liima zu erhalten: viewer, server_admin, config_admin, shakedown_admin, app_developer.
Die Rollen werden in Deployment- und Konfigurationsrollen unterschieden. Deployment Rollen kontorollieren ob der User Deployments auf bestimmten Umgebung confirmen können, Konfigurationsrollen bestimmen was konfiguriert werden kann, auch Liima Settings.

|Rolle              		 |Deployment Rolle  |Beschrieb										|
|------------------------|------------------|------------------------------------------------------------------------------------|
|admin_deployer		       |x                 | Deploy Rechte auf Produktion, Request Rechte auf allen anderen |
|admin_developer		     |                  | Kann Ressourcen von Typ ApplicationServer und Application erstelle sowie CopyFrom dort ausführen. Kann ebenfalls auf ApplicationServers Relations verwalten.
|app_developer		       |                  | Hat volle Rechte auf Ressourcen vom Type Application. Darf Passwörter nicht sehen/editieren.  |
|config_admin		         |                  | Enthält alle zusätzlichen Rechte die zur Administration nötig sind, die nicht schon in den anderen Konfigurationsrollen enthalten sind.|
|developer_deployer	     |x                 | Confirm Rechte auf Entwicklungsumgebungen, Request rechte auf allen anderen |
|env_management_deployer |x                 | Confirm Rechte auf Integrationsumgebungen, Request rechte auf allen anderen  |
|overwrite_foreignables	 |                  | |
|server_admin		         |                  | Kann Default Ressourcen erstellen und löschen sowie Relations von ApplicationServer hinzufügen und löschen.|
|shakedown_admin		     |                  | Kann Shakedown Test konfigurieren |
|shakedown_tester		     |                  | Kann Shakedown Tests durchführen |
|test_deployer		       |x                 | Confirm Rechte auf Testumgebungen, Request Rechte auf allen anderen |
|viewer		               |                  | Nur Leserechte. Darf Passwörter nicht sehen, kann keine Request erstellen, darf Testgenerate nicht aufrufen |

## Caching
Sämtliche Permissions werden in einem Singelton gecached, welcher alle 20 Minuten oder beim Hinzufügen, Verändern oder Löschen einer Permission aktualisiert wird.  
Die neuem gescopten Permissions werden momentan nur wo nötig in der JSF User Sessions gecached. Das bedeutet, dass Änderungen an den Permissions `RESOURCE & RESOURCETYPE (READ & DELETE)` erst sichtbar werden, nach dem eine neue Session für den User erstellt wurde (durch Logout oder nach Ablauf der Session).
