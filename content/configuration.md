# Konfiguration

In diesem Kapitel wird beschrieben wie einerseits der Applikationserver und andererseits die Applikation AMW konfiguriert werden kann.

## Applikationserver Konfiguration (standalone.xml)

### Datasource:

AMW verwendet eine Datenbank um das Modell und die Konfiguration persistent abzuspeichern. Die Datenbank wird über eine Datasource im standalone.xml im entsprechenden Applikationsserver konfiguriert.

Dafür muss eine Datasource mit dem Namen **amwDS** ``java:jboss/datasources/amwDS`` eingerichtet werden.
Soll AMW die Datenbankchanges beim Starten selber einspielen, muss dafür zusätzlich die Datasource **amwLiquibaseDS** ``java:jboss/datasources/amwLiquibaseDS`` eingerichtet sein.

``` XML
<subsystem xmlns="urn:jboss:domain:datasources:1.2">
   <datasources>
      <xa-datasource jndi-name="java:jboss/datasources/amwDS" pool-name="amwDS" use-java-context="true" use-ccm="true">
         <driver>oracle</driver>
         <xa-datasource-property name="URL">jdbc:oracle:thin:@//server:1521/schema</xa-datasource-property>
         <xa-datasource-property name="driverType">4</xa-datasource-property>
         <security>
            <user-name>User</user-name>
            <password>Password</password>
         </security>
         <xa-pool>
            <min-pool-size>0</min-pool-size>
            <max-pool-size>30</max-pool-size>
            <prefill>false</prefill>
            <is-same-rm-override>false</is-same-rm-override>
            <no-tx-separate-pools>true</no-tx-separate-pools>
         </xa-pool>
         <validation>
            <background-validation-millis>60000</background-validation-millis>
            <validate-on-match>false</validate-on-match>
            <background-validation>true</background-validation>
            <valid-connection-checker class-name="org.jboss.jca.adapters.jdbc.extensions.oracle.OracleValidConnectionChecker" />
            <stale-connection-checker class-name="org.jboss.jca.adapters.jdbc.extensions.oracle.OracleStaleConnectionChecker" />
            <exception-sorter class-name="org.jboss.jca.adapters.jdbc.extensions.oracle.OracleExceptionSorter" />
         </validation>
      </xa-datasource>
      
      <xa-datasource jndi-name="java:jboss/datasources/amwLiquibaseDS" pool-name="amwLiquibaseDS" use-java-context="true" use-ccm="true">
         <driver>oracle</driver>
         <xa-datasource-property name="URL">jdbc:oracle:thin:@//server:1521/schema</xa-datasource-property>
         <xa-datasource-property name="driverType">4</xa-datasource-property>
         <security>
            <user-name>User</user-name>
            <password>Password</password>
         </security>
         <xa-pool>
            <min-pool-size>0</min-pool-size>
            <max-pool-size>30</max-pool-size>
            <prefill>false</prefill>
            <is-same-rm-override>false</is-same-rm-override>
            <no-tx-separate-pools>true</no-tx-separate-pools>
         </xa-pool>
         <validation>
            <background-validation-millis>60000</background-validation-millis>
            <validate-on-match>false</validate-on-match>
            <background-validation>true</background-validation>
            <valid-connection-checker class-name="org.jboss.jca.adapters.jdbc.extensions.oracle.OracleValidConnectionChecker" />
            <stale-connection-checker class-name="org.jboss.jca.adapters.jdbc.extensions.oracle.OracleStaleConnectionChecker" />
            <exception-sorter class-name="org.jboss.jca.adapters.jdbc.extensions.oracle.OracleExceptionSorter" />
         </validation>
      </xa-datasource>
      <drivers>
         <driver name="oracle" module="com.oracle">
            <xa-datasource-class>oracle.jdbc.xa.client.OracleXADataSource</xa-datasource-class>
         </driver>
      </drivers>
   </datasources>
</subsystem>
```

**Wichtig:** die amwLiquibaseDS Datasource muss auf die gleiche Datenbank zeigen. Der einzige Unterschied kann der Datenbank User sein, falls man die Berechtigungen so einstellen will, dass nur der User der amwLiquibaseDS Datasource Rechte hat um Schemaändergungen einzuspielen.

Wird das System Property ``liquibase.enabled`` auf false geestellt, ist Liquibase ausgeschaltet und die Datasource amwLiquibaseDS muss nicht definiert werden.

#### JDBC Driver

In der obigen Datasource Konfiguratio ist der Treiber via JBoss Module Mechanismus deployed werden: https://access.redhat.com/documentation/en-US/JBoss_Enterprise_Application_Platform/6/html/Administration_and_Configuration_Guide/Example_Oracle_Datasource.html

Als zweite Variante kann der JDBC Driver ``ojdbc6_g.jar`` auch nach ``standalone/deployemnts`` kopiert werden und mittels ``<driver>ojdbc6_g.jar</driver>`` darauf verwiesen werden.

### Mail Session

AMW versendet bei Deployments an interessierte User Informationen über den Status der entsprechenden Deployments, dafür benötigt AMW eine MailSession mit dem Namen **AutomationMiddlewareMail** ``java:/AutomationMiddlewareMail``

So muss im ``<subsystem xmlns="urn:jboss:domain:mail:1.1">`` die folgende Mailsession eingerichtet werden.

``` XML
<subsystem xmlns="urn:jboss:domain:mail:1.1">
    <mail-session jndi-name="java:/AutomationMiddlewareMail" debug="true">
        <smtp-server outbound-socket-binding-ref="mail-smtp"/>
    </mail-session>
</subsystem>
```

Falls der Applikationserver sich beim Versenden von Emails beim SMTP Server anmelden muss, und sich der smtp server von der default konfig unterscheidet, muss allenfalls auch ein zusätzlicher smtp Server eingerichtet werden

Unter socket-binding-group
``` XML
<outbound-socket-binding name="mail-smtp-amw">
    <remote-destination host="mail.example.com" port="465"/>
</outbound-socket-binding>
```

und entsprechend die Mail Session

``` XML
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

``` XML
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

``` XML
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

