Release 0.8.3.1 (September 20, 2017)
====================================

Highlights
----------
- Optimize feed creation in NiFi and improve NiFi usability when there is a large number of feeds
- Ability to skip NiFi auto alignment when saving feeds
- Fix bug in operations manager that didn't correctly fail jobs
- Support for 'failure connection' detection in feeds that contain sub process groups
- Fixes KYLO-823, KYLO-1202 setting controller service properties in feed/reusable templates
- Follow targetURL when logging in
- Fix Hive impersonation bug
- Additional metadata indexing to increase Kylo performance


Download Links
--------------

 - RPM : `<http://bit.ly/2xgHsUM>`__

 - Debian : `<http://bit.ly/2hhqKOG>`__

 - TAR : `<http://bit.ly/2xT9ExY>`__

Upgrade Instructions from v0.8.3
--------------------------------

Build or `download the RPM <http://bit.ly/2xgHsUM>`__

1. Uninstall Kylo:

 .. code-block:: shell

   /opt/kylo/remove-kylo.sh

 ..

2. Install the new RPM:

 .. code-block:: shell

     rpm –ivh <RPM_FILE>

 ..

3. Copy the application.properties file from the 0.8.2 install.  If you have customized the application.properties file you will want to copy the 0.8.2 version and add the new properties that were added for this release.

     3.1 Find the /bkup-config/TIMESTAMP/kylo-services/application.properties file

        - Kylo will backup the application.properties file to the following location, */opt/kylo/bkup-config/YYYY_MM_DD_HH_MM_millis/kylo-services/application.properties*, replacing the "YYYY_MM_DD_HH_MM_millis" with a valid time:

     3.2 Copy the backup file over to the /opt/kylo/kylo-services/conf folder

        .. code-block:: shell

          ### move the application.properties shipped with the .rpm to a backup file
          mv /opt/kylo/kylo-services/application.properties application.properties.0_8_2_2_template
          ### copy the backup properties  (Replace the YYYY_MM_DD_HH_MM_millis  with the valid timestamp)
          cp /opt/kylo/bkup-config/YYYY_MM_DD_HH_MM_millis/kylo-services/application.properties /opt/kylo/kylo-services/conf

        ..

     3.3 Optional: If you want to skip the auto alignment after saving feeds then add in the new properties to the /opt/kylo/kylo-services/application.properties file

         .. code-block:: shell

                ## skip auto alignment after you create a feed.
                ##You can always manually align your flows in NiFi via a Kylo Rest Endpoint
                nifi.auto.align=false
         ..

        Optional: At startup Kylo inspects NiFi to build a cache of NiFi flow data. It now does this with multiple threads.  By default it uses 10 threads.  You can modify this by setting the following property:

         .. code-block:: shell

                ## Modify the number of threads used by Kylo at startup to inspect and build the NiFi flow cache.  Default is 10 if not specified
                nifi.flow.inspector.threads=10

         ..

     3.4 Ensure the property ``security.jwt.key`` in both kylo-services and kylo-ui application.properties file match.  They property below needs to match in both of these files:

         - */opt/kylo/kylo-ui/conf/application.properties*
         - */opt/kylo/kylo-services/conf/application.properties*.

       .. code-block:: properties

         security.jwt.key=

       ..

4. Update the NiFi nars.  Run the following shell script to copy over the new NiFi nars/jars to get new changes to NiFi processors and services.

   .. code-block:: shell

      /opt/kylo/setup/nifi/update-nars-jars.sh <NIFI_HOME> <KYLO_SETUP_FOLDER> <NIFI_LINUX_USER> <NIFI_LINUX_GROUP>

      Example:  /opt/kylo/setup/nifi/update-nars-jars.sh /opt/nifi /opt/kylo/setup nifi users
   ..

5. After you startup you may need to re-index the Kylo metadata.  You can do this via a REST endpoint after you login to Kylo at the following url:

       http://localhost:8400/proxy/v1/metadata/debug/jcr-index/reindex



