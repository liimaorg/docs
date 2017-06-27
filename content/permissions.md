# Berechtigungen


|Berechtigung		|Action		|Beschrieb										|
|-----------------------|---------------|---------------------------------------------------------------------------------------|
|DEPLOYMENT		|ALL		|Darf deployen, die Action hat keinen Einfluss						|
|COPY_FROM_RESOURCE	|UPDATE		|Darf die Eigenschaften von einer Resource oder eines Predecessor kopieren		|
|RELEASE		|CREATE		|Darf speichern										|
|			|READ		|Darf betrachten									|
|			|UPDATE		|Darf speichern										|
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

## Caching
Die alten globalen Permissions werden in einem Singelton gecached, dass alle 20 Minuten updated wird.
Die neue gescopten Permissions werden momentan nur wo nötig in der JSF User Sessions gecached. Das bedeutet, dass Änderungen erst sichtbar sind, nach dem eine neue Session für den User erstellt wurde (durch Logout oder nach Ablauf der Session).
