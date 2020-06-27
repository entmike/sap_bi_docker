FROM centos:centos8 AS preinstall

LABEL author="Mike Howles" \
      install="SAP Business Objects 4.3" \
      platform="Centos 8" \
      license="Apache 2.0" \
      description="Docker file for SAP Business Objects" \
      terms="Use at your own risk."

USER root

ARG license_key
ARG password

# Prerequisite
RUN yum install -y glibc-2.28-101.el8.i686 libstdc++-8.3.1-5.el8.0.2.x86_64 libstdc++-8.3.1-5.el8.0.2.i686 libnsl-2.28-101.el8.i686 libcrypt.so.1 xz-libs-5.2.4-3.el8.i686 libnsl-2.28-101.el8.i686 libnsl.x86_64 

ENV LANG=en_US.utf8
ENV LC_ALL=en_US.utf8

# Copy install media
ADD install/ /source

RUN useradd -ms /bin/bash bi
RUN mkdir /bi
RUN chown bi /bi

USER bi
ENV LANG=en_US.utf8
ENV LC_ALL=en_US.utf8

RUN echo $HOSTNAME >> /bi/currhost.txt && /source/setup.sh -q -InstallDir /bi \
chooseintroscopeintegration=nointegrate \
choosesmdintegration=nointegrate \
clusterkey=$password \
cmspassword=$password \
cmsport=6400 \
installdir=/bi/ \
installtype=default \
neworexistinglcm=existing \
productkey=$license_key \
selectedlanguagepacks=en \
selectintegrateddatabase=1 \
setupuilanguage=en \
sianame=localhost \
siaport=6410 \
sqlanywhereadminpassword=$password \
sqlanywhereport=2638 \
tomcatconnectionport=8080 \
tomcatredirectport=8443 \
tomcatshutdownport=8005 \
wacsport=6405 \
webappservertype=tomcat \
features=JavaWebApps1,MobileServers,MobileAddon,MobileServices,IntegratedTomcat,WebTier,CMS,FRS,PlatformServers.IntegratedDB.SQLAnywhere,PlatformServers.EventServer,AdaptiveProcessingServer,AdaptiveJobServer,Platform.RestWebService,Platform.Action.Framework.backend,ConnectionProcService,ConnectionServices,DataFederatorQueryService,DataFederatorServices,MultidimensionalAnalysisServices,BExWebApplicationsService,AdvancedAnalysisServices,CrystalReportsProcServices,CrystalReportSchedulingServices,CrystalReport2011ProcServices,CrystalReport2011SchedulingServices,CrystalReportsServers,WebIProcServer,WebISchedulingServices,WebIServers,XcelsiusServers,BWPublisherServer,IntegrationServers,UpgradeManager,Automation.Framework,PromotionManagementWizard,MultitenancyManager,AdministratorTools,SL_SDK_java,SL_SDKSamples,SL_SDK,DataAccess.DataFederator,DataAccess.HPVertica,DataAccess.MySQL,DataAccess.GenericJDBC,DataAccess.GenericOLEDB,DataAccess.OptionalDataDirectODBC.DataDirect7.1,DataAccess.MaxDB,DataAccess.SAPHANA,DataAccess.Snowflake,DataAccess.Salesforce,DataAccess.Netezza,DataAccess.Microsoft_AnalyticalServices,DataAccess.MicrosoftExchange,DataAccess.MicrosoftOutlook,DataAccess.Microsoft_SQLServer,DataAccess.Microsoft_Access,DataAccess.Ingres,DataAccess.Greenplum,DataAccess.PostgreSQL,DataAccess.Progress,DataAccess.IBMDB2,DataAccess.Informix,DataAccess.Oracle,DataAccess.Sybase,DataAccess.Teradata,DataAccess.SAPBW,DataAccess.SAPERP,DataAccess.XMLWebServices,DataAccess.OData,DataAccess.SAP,DataAccess.PersonalFiles,DataAccess.JavaBean,DataAccess.OpenConnectivity,DataAccess.HadoopHive,DataAccess.Amazon,DataAccess.Spark,DataAccess.Hortonworks,DataAccess.Essbase,DataAccess.Peoplesoft,DataAccess.JDEdwards,DataAccess.OracleEBS,DataAccess.Universe,DataAccess.MyCube,DataAccess.XML,DataAccess.ADO.NET,DataAccess.COMData,DataAccess.DataSet,DataAccess.SymantecACT,DataAccess.BDE,DataAccess.CDO,DataAccess.FieldDefinitions,DataAccess.FileSystem,DataAccess.NTEventLog,DataAccess.WebActivityLog,DataAccess.Btrieve,DataAccess.dBase.DataDirect7.1,DataAccess.UWSC,DataAccess.CMSDBDriver,Samples && \
sed -i -- 's/'$(cat /bi/currhost.txt)'/localhost/g' /bi/sap_bobj/ccm.config && \
sed -i -- 's/'$(cat /bi/currhost.txt)'/localhost/g' /bi/sap_bobj/tomcat/webapps/BOE/WEB-INF/config/default/* &&\
rm /bi/currhost.txt

USER root

# Remove unnecessary files to reduce image size
RUN rm -Rf /bi/InstallData &&\
rm -Rf /bi/sap_bobj/tomcat/work/Catalina/*

USER bi

FROM centos:centos8

LABEL author="Mike Howles" \
      install="SAP Business Objects 4.3" \
      platform="Centos 8" \
      license="Apache 2.0" \
      description="Docker file for SAP Business Objects" \
      terms="Use at your own risk."

USER root

# Prerequisite
RUN yum install -y glibc-2.28-101.el8.i686 libstdc++-8.3.1-5.el8.0.2.x86_64 libstdc++-8.3.1-5.el8.0.2.i686 libnsl-2.28-101.el8.i686 libcrypt.so.1 xz-libs-5.2.4-3.el8.i686 libnsl-2.28-101.el8.i686 libnsl.x86_64 

ENV LANG=en_US.utf8
ENV LC_ALL=en_US.utf8

RUN useradd -ms /bin/bash bi
COPY --from=preinstall /bi /bi

USER bi
ENV LANG=en_US.utf8
ENV LC_ALL=en_US.utf8

# Ports
EXPOSE 8080 6400 6405

# Persistence Volumes
VOLUME /bi/sqlanywhere/database
VOLUME /bi/sap_bobj/logging
VOLUME /bi/sap_bobj/data

CMD /bi/sap_bobj/sqlanywhere_startup.sh && /bi/sap_bobj/tomcatstartup.sh && /bi/sap_bobj/startservers && tail -f /dev/null
