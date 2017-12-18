# Berechtigungen
Die Berechtigungen in Liima sind in global/ungescopte Permissions und scoped Permissions aufgeteilt.

## Scoped Permissions
Scoped Permissions tragen den Namen eines Liima Entität Typs (z.B. `RESOURCE`) oder eines Subtyps (z.B. `RESOURCE_TEMPLATE`). Berechtigungen auf den Entitäten können weiter eingeschränkt werden: 
* Action: Aktion die auf der Entität ausgeführt werden kann: `CREATE`, `READ`, `UPDATE`, `DELETE` oder `ALL`. Falls die Aktionen auf der Entität nicht anwendbar sind, wird `ALL` verwendet.
* Environment: Einschränkung auf Umgebungen
* Resource Type Categories: Kategorie von Ressourcen auf welche die Permission gelten soll. 
  * `DEFAULT_ONLY`: Einschränkung auf Default Ressourcen: `APPLICATIONSERVER`, `APPLICATION`, `NODE`
  * `NON_DEFAULT_ONLY`: alle ausser die Default Ressourcen
  * `ANY`: keine Einschränkung
* Resource Type: Ressource Typ auf welche die Berechtigung eingeschränkt werden soll, z.B. Ressource Typ `Webservice`
* Resource Group: Einschränkung auf eine Ressource Gruppe, z.B. die Applikation `TestApp`

`Resource Type Categories`, `Resource Type` und `Resource Group` sind hierarchisch aufgebaut und schliessen sich gegenseitig aus. Z.B. wenn eine `Resource Type Categories` ausgewählt ist, kann `Resource Type` und `Resource Group` nicht mehr angegeben werden.

## Global Permissions
Global Permissions tragen den Namen von einer Aktion auf ein Liima Objekt (z.B. `SAVE_SETTINGS_PROPTYPE`). Diese Permissions können nicht weiter eingeschränkt werden d.h. sind global. Im Permission GUI sind diese mit `Global` markiert. 
Die globalen Permissions Regeln nur noch was auf dem JSF GUI angezeigt wird, einzelne alte Berechtigungen, Permissions welche Liima Settings betreffen und die noch nicht auf Scoped Permissions migriert wurden.

### Selbstverwaltung von Permissions
Rollen oder Benutzer, welche die globale Permission `PERMISSION_DELEGATION` besitzen, können ihnen gewährte Berechtigungen an andere Benutzer weitergeben.

Rollen oder Benutzer, welche die globale Permission `ADD_ADMIN_PERMISSIONS_ON_CREATED_RESOURCE` besitzen, erhalten automatisch "Resourcen-Administrationsberechtigungen" auf allen Resourcen, welche sie selber erstellen.

Resourcen-Adiminstrationsberechtigungen: `RESOURCE`, `RESOURCE_*` und `DEPLOYMENT` - jeweils mit Action `ALL`.

## Scoped Permissions und ihre Bedeutung

|Berechtigung		|Action		|Beschrieb										|
|-----------------------|---------------|---------------------------------------------------------------------------------------|
|DEPLOYMENT		|CREATE		|Darf Deployment **Requests** erstellen				|
|			|READ		  |Darf die Deployment Seite sehen			|
|			|UPDATE		|Darf Deployment Requests confirment und das Datum updaten		|
|			|DELETE  |Darf Deployments rejecten |
|RELEASE (settings)		|CREATE		|Darf erstellen							|
|			|READ		|Darf betrachten									|
|			|UPDATE		|Darf editieren										|
|			|DELETE		|Darf löschen										|
|RESOURCE		|CREATE		|Darf Resourcen erstellen								|
| 		|READ		|Darf Resourcen betrachten								|
| 	 |UPDATE		|Darf Resourcen, deren Relations und Properties editieren			|
|			|		|Damit Consumed/Provided Relations hinzugefügt werden können, wird zusätzlich eine RESOURCE READ Berechtigung für die Slave-Resource benötigt	|
|			|		|Um Functions einer Resource zu betrachten/erstellen/editieren/löschen wird eine entsprechende RESOURCE_AMWFUNCTION Berechtigung benötigt	|
|			|		|Um Passwörter im Klartext zu sehen, wird eine RESOURCE_PROPERTY_DECRYPT Berechtigung benötigt	|
|			|		|Um Templates zu editieren/hinzufügen/löschen, wird eine RESOURCE_TEMPLATE Berechtigung benötigt	|
|RESOURCETYPE		|READ		|Darf ResourceTypen betrachten								|
|			|UPDATE		|Darf ResourceTypen, deren Relations und Properties editieren		|
|			|		|Damit Type Relations hinzugefügt werden können, wird zusätzlich eine RESOURCETYPE READ Berechtigung für die Slave-Resource benötigt	|
|			|		|Um Functions eines ResourceType zu betrachten/erstellen/editieren/löschen wird eine entsprechende RESOURCETYPE_AMWFUNCTION Berechtigung benötigt	|
|			|		|Um Passwörter im Klartext zu sehen, wird eine RESOURCETYPE_PROPERTY_DECRYPT Berechtigung benötigt	|
|			|		|Um Templates zu editieren/hinzufügen/löschen, wird eine RESOURCETYPE_TEMPLATE Berechtigung benötigt	|
|RESOURCE_AMWFUNCTION		|CREATE		|Darf Functions für Resourcen erstellen								|
|			|READ		|Darf Functions betrachten								|
|			|UPDATE		|Darf Functions bearbeiten								|
|			|DELETE		|Darf Functions löschen									|
|RESOURCETYPE_AMWFUNCTION		|CREATE		|Darf Functions für ResourceTypen erstellen								|
|			|READ		|Darf Functions betrachten								|
|			|UPDATE		|Darf Functions bearbeiten								|
|			|DELETE		|Darf Functions löschen									|
|RESOURCE_PROPERTY_DECRYPT		|ALL		|Darf Passwörter im Klartext sehen/editieren								|
|  		| 	|Um die Verschlüsselung von Properties ein oder auszuschalten, darf die Berechtigung nicht auf eine spezifische Umgebung eingeschränkt sein 								|
|RESOURCETYPE_PROPERTY_DECRYPT		|ALL		|Darf Passwörter im Klartext sehen/editieren								|
|  		| 	|Um die Verschlüsselung von Properties ein oder auszuschalten, darf die Berechtigung nicht auf eine spezifische Umgebung eingeschränkt sein 								|
|RESOURCE_TEMPLATE	|CREATE		|Darf Resource Templates erstellen							|
|  	|READ		|Darf Resource Templates betrachten							|
|			|UPDATE		|Darf Resource Templates editieren							|
|			|DELETE		|Darf Resource Templates löschen							|
|RESOURCETYPE_TEMPLATE	|CREATE		|Darf ResourceTyp Templates erstellen							|
|   |READ		|Darf ResourceTyp Templates betrachten							|
|			|UPDATE		|Darf ResourceTyp Templates editieren							|
|			|DELETE		|Darf ResourceTyp Templates löschen							|
|RESOURCE_RELEASE_COPY_FROM_RESOURCE	|ALL		|Darf die Eigenschaften von einer Resource oder eines Predecessor kopieren		|
|   	|   | Es wird zusätzlich eine RESOURCE READ Berechtigung auf der Source-Resource benötigt		|
|RESOURCE_TEST_GENERATION		|READ		|Darf Testgeneration starten										|
|RESOURCE_TEST_GENERATION_RESULT		|READ		|Darf die erstellten Tempaltes einer Testgenration sehen.										|
|SHAKEDOWNTEST		|CREATE		|Darf erstellen										|
|			|UPDATE		|Darf editieren										|
|			|DELETE		|Darf löschen										|


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
