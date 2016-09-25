# Konfiguration

In diesem Kapitel wird beschrieben wie einerseits der Applikationserver und andererseits die Applikation AMW konfiguriert werden kann.

## Applikationserver Konfiguration (standalone.xml)

### Datasource:

AMW verwendet eine Datenbank um das Modell und die Konfiguration persistent abzuspeichern. Die Datenbank wird über eine Datasource im standalone.xml im entsprechenden Applikationsserver konfiguriert.

Dafür muss eine Datasource mit dem Namen **amwDS** ``java:jboss/datasources/amwDS`` eingerichtet werden:

```
<datasource jndi-name="java:jboss/datasources/amwDS" pool-name="java:jboss/datasources/amwDS_Pool" enabled="true" use-java-context="true" use-ccm="true">
    <connection-url>jdbc:oracle:thin:@//server:1521/schema</connection-url>
    <driver>ojdbc6_g.jar</driver>
    <security>
        <user-name>Username</user-name>
        <password>Password</password>
    </security>
</datasource>
```

Soll AMW die Datenbankchanges beim Starten selber einspielen, muss dafür die Datasource **amwLiquibaseDS** ``java:jboss/datasources/amwLiquibaseDS`` entsprechend eingerichtet sein.

```
<datasource jndi-name="java:jboss/datasources/amwLiquibaseDS" pool-name="java:jboss/datasources/amwLiquibaseDS_Pool" enabled="true" use-java-context="true" use-ccm="true">
    <connection-url>jdbc:oracle:thin:@//server:1521/schema</connection-url>
    <driver>ojdbc6_g.jar</driver>
    <security>
        <user-name>Username</user-name>
        <password>Password</password>
    </security>
</datasource>
```

**Wichtig:** die Datasource muss auf die gleiche Datenbank zeigen. Der einzige Unterschied kann der Datenbank User sein, falls man die Berechtigungen so einstellen will, dass nur der User der amwLiquibaseDS Datasource Rechte hat um Schemaändergungen einzuspielen.

Existiert die Datasource amwLiquibaseDS nicht, werden keine Datenbank Changesets eingespielt. 

Wird das System Property ``liquibase.enabled`` auf false geestellt, werden die Changes auch nicht eingespielt.

#### JDBC Driver

In der obigen Datasource Konfiguration ist mittels ``<driver>ojdbc6_g.jar</driver>`` definiert, dass die Datasource direkt unter ``standalone/deployemnts`` ein Jar File ``ojdbc6_g.jar`` erwartet, das den Treiber beinhaltet.

Als zweite Variante kann der JDBC Driver auch über den JBoss Module Mechanismus deployed werden: https://access.redhat.com/documentation/en-US/JBoss_Enterprise_Application_Platform/6/html/Administration_and_Configuration_Guide/Example_Oracle_Datasource.html

### Mail Session

AMW versendet bei Deployments an interessierte User Informationen über den Status der entsprechenden Deployments, dafür benötigt AMW eine MailSession mit dem Namen **AutomationMiddlewareMail** ``java:/AutomationMiddlewareMail``

So muss im ``<subsystem xmlns="urn:jboss:domain:mail:1.1">`` die folgende Mailsession eingerichtet werden.

```
<subsystem xmlns="urn:jboss:domain:mail:1.1">
    <mail-session jndi-name="java:/AutomationMiddlewareMail" debug="true">
        <smtp-server outbound-socket-binding-ref="mail-smtp"/>
    </mail-session>
</subsystem>
```

Falls der Applikationserver sich beim Versenden von Emails beim SMTP Server anmelden muss, und sich der smtp server von der default konfig unterscheidet, muss allenfalls auch ein zusätzlicher smtp Server eingerichtet werden

Unter socket-binding-group
```
<outbound-socket-binding name="mail-smtp-amw">
    <remote-destination host="mail.example.com" port="465"/>
</outbound-socket-binding>
```

und entsprechend die Mail Session

```
<subsystem xmlns="urn:jboss:domain:mail:1.1">
    <mail-session jndi-name="java:/AutomationMiddlewareMail" debug="true">
        <smtp-server outbound-socket-binding-ref="mail-smtp-amw">
            <login name="example@example.com" password="passwd"/>
        </smtp-server>
    </mail-session>
</subsystem>
```

### Authentisierung

AMW Implementiert ein Rollenmodell, das direkt in die JBoss Authentisierung integriert ist. Diese muss entsprechend im Subsystem ``urn:jboss:domain:security:1.2`` konfiguriert werden.

#### Username Passwort File (PoC Umgebungen)

über die beiden Files JBOSS_HOME/standalone/configuration/application-users.properties und JBOSS_HOME/standalone/configuration/application-roles.properties können die User konfiguriert werden.

Diese Integration sollte bloss für Proof Of Concept oder Testinstalltionen verwendet werden.

```
<security-domain name="jboss-secure">
    <authentication>
        <login-module code="org.jboss.security.auth.spi.UsersRolesLoginModule" flag="required">
            <module-option name="usersProperties" value="${jboss.server.config.dir}/application-users.properties"/>
            <module-option name="rolesProperties" value="${jboss.server.config.dir}/application-roles.properties"/>
        </login-module>
    </authentication>
</security-domain>
```

In AMW stehen die folgenden Rollen zur Verfügung:

* viewer
* config_admin
* app_developer
* server_admin
* admin_deployer
* test_deployer
* developer_deployer
* env_management_deployer
* shakedown_tester
* shakedown_admin
* overwrite_foreignables

#### Produktive Umgebungen

Für produktive Umgebungen sollte der JBoss entsprechend an ein LDAP oder ActiveDirectory angebunden werden.


## Konfiguration der Applikation

Die folgende Konfiguration wird der Applikation über Java Systemproperties zur Verfügung gestellt, diese können im standalone.xml unter server --> system-properties konfiguriert werden

```
<system-properties>
    <property name="amw.generatorPath" value="/tmp/amw/gen"/>
    ...
</system-properties>
```

### AMW Konfiguration

| Key                                       | Optional | Description                                                       | Default  |
| ----------------------------------------- |:--------:| ----------------------------------------------------------------- | -------- |
| amw.logsPath                              |          | Path where Deploymentlogs are written to                          |          |
| amw.logsLeanupAge                         |    x     | Age of logs to be deleted in minutes                              | 10080    |
| amw.logsCleanupSchedulerDisabled          |    x     | Whether the logs cleanup Scheduler is disabled or not             | false    |
| amw.generatorPath                         |          | Path where the generator writes the files                         |          |
| amw.generatorPath.simulation              |          | Path where the generator writes the files for simulations         |          |
| amw.generatorPath.test                    |          | Path where the generator writes the files for test modus          |          |
| amw.mailDomain                            |          | Mail domain which gets added to the userid to send notifications  |          |
| amw.deliverMail                           |          | set to false to disable Maildelivery                              |          |
| amw.encryptionKey                         |          | EncryptionKey for encrypted Property Values                       |          |
| amw.logoutUrl                             |          | LogoutURL                                                         |          |
| amw.stmpath                               |          |                                                                   |          |
| amw.stmrepo                               |          |                                                                   |          |
| amw.testResultPath                        |          |                                                                   |          |
| amw.deploymentInProgressTimeout           |    x     | How long a Deployment can be in progress until it is stoped in s  | 3600     |
| amw.predeploymentInProgressTimeout        |    x     | Timeout for Predeployments                                        | 7200     |
| amw.deploymentProcessingAmountPerRun      |    x     | How many deployments are executed during one scheduler run        | 5        |
| amw.deploymentSimulationAmountPerRun      |    x     | How many simulations are executed during one scheduler run        | 5        |
| amw.deploymentPredeploymentAmountPerRun   |    x     | How many predeployments are executed during one scheduler run     | 5        |
| amw.deploymentSchedulerDisabled           |    x     | Whether the deployment scheduler is disabled or not               | false    |
| amw.deploymentCleanupSchedulerDisabled    |    x     | Whether the cleanup scheduler is disabled or not                  | false    |
| amw.deploymentCleanupAge                  |    x     | Age of folders to be deleted in minutes                           | 240      |
| amw.vmDetailUrl                           |    x     |                                                                   |          |
| amw.vmUrlParam                            |    x     |                                                                   |          |
| amw.csvSeparator                          |    x     |                                                                   | ;        |
| amw.localEnv                              |    x     | Define the local Environment                                      | Local    |
| amw.providableSoftlinkResourceTypes       |    x     | Commaseparated List of Resource Types                             |          |
| amw.consumableSoftlinkResourceTypes       |    x     | Commaseparated List of Resource Types                             |          |
| amw.externalResourceBacklinkSchema        |    x     | http/https                                                        |          |
| amw.externalResourceBacklinkHost          |    x     | host for the backlink                                             |          |

