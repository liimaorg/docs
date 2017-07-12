# Berechtigungen


|Berechtigung		|Action		|Beschrieb										|
|-----------------------|---------------|---------------------------------------------------------------------------------------|
|DEPLOYMENT		|ALL		|Darf deployen, die Action hat keinen Einfluss. Wer darf Request machen?						|
|COPY_FROM_RESOURCE	|UPDATE		|Darf die Eigenschaften von einer Resource oder eines Predecessor kopieren		|
|RELEASE (settings)		|CREATE		|Darf erstellen							|
|			|READ		|Darf betrachten									|
|			|UPDATE		|Darf editieren										|
|			|DELETE		|Darf löschen										|
|SHAKEDOWNTEST		|CREATE		|Darf erstellen										|
|			|UPDATE		|Darf editieren										|
|			|DELETE		|Darf löschen										|
|RESOURCE		|UPDATE		|Darf Resourcen, deren Relations, Properties und Templates editieren			|
|			|		|Um PropertyDescriptoren einer Resource zu erstellen/editieren/löschen wird die SAVE_PROPERTY Berechtigung benötigt	|
|			|		|Um Functions einer Resource zu betrachten/erstellen/editieren/löschen wird eine entsprechende AMWFUNCTION Berechtigung benötigt	|
|			|		|Um Passwörter im Klartext zu sehen, wird eine DECRYPT_PROPERTIES Berechtigung benötigt	|
|RESOURCETYPE		|READ		|Darf ResourceTypen betrachten								|
|			|UPDATE		|Darf ResourceTypen, deren Relations, Properties und Templates editieren		|
|			|		|Um PropertyDescriptoren eines ResourceType zu erstellen/editieren/löschen wird die SAVE_PROPERTY Berechtigung benötigt		|
|			|		|Um Functions eines ResourceType zu betrachten/erstellen/editieren/löschen wird eine entsprechende AMWFUNCTION Berechtigung benötigt	|
|			|		|Um Passwörter im Klartext zu sehen, wird eine DECRYPT_PROPERTIES Berechtigung benötigt	|
|TEMPLATE_RESOURCE	|READ		|Darf Resource Templates betrachten							|
|			|UPDATE		|Darf Resource Templates editieren							|
|			|DELETE		|Darf Resource Templates löschen							|
|TEMPLATE_RESOURCETYPE	|READ		|Darf ResourceTyp Templates betrachten							|
|			|UPDATE		|Darf ResourceTyp Templates editieren							|
|			|DELETE		|Darf ResourceTyp Templates löschen							|
|AMWFUNCTION		|CREATE		|Darf Functions erstellen								|
|			|READ		|Darf Functions betrachten								|
|			|UPDATE		|Darf Functions bearbeiten								|
|			|DELETE		|Darf Functions löschen									|

## Default Rollen
In Liima sind einge default Rollen definiert. Diese sind so aufgebaut, dass sie miteinander kombiniert werden müssen um eine komplette Rolle zu ergeben, z.B. ein Admin Benutzer der braucht folgende Rollen um alle config Rechte in Liima zu haben: viewer, server_admin, config_admin, shakedown_admin, admin_deployer

|Rolle              		 |Deployment Rolle  |Beschrieb										|
|------------------------|------------------|------------------------------------------------------------------------------------|
|admin_deployer		       |x                 | |
|admin_developer		     |                  | |
|app_developer		       |x                 | |
|config_admin		         |                  | |
|developer_deployer	     |x                 | |
|env_management_deployer |x                 | |
|overwrite_foreignables	 |                  | |
|server_admin		         |                  | |
|shakedown_admin		     |                  | |
|shakedown_tester		     |                  | |
|test_deployer		       |x                 | |
|viewer		               |                  | |

## Caching
Sämtliche Permissions werden in einem Singelton gecached, welcher alle 20 Minuten oder beim Hinzufügen, Verändern oder Löschen einer Permission aktualisiert wird.  
Die neuem gescopten Permissions werden momentan nur wo nötig in der JSF User Sessions gecached. Das bedeutet, dass Änderungen an den Permissions `RESOURCE & RESOURCETYPE (READ & DELETE)` erst sichtbar werden, nach dem eine neue Session für den User erstellt wurde (durch Logout oder nach Ablauf der Session).
