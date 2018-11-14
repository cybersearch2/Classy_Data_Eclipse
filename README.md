# Classy Data Eclipse Plug-in

This project brings to Eclipse the [Classy Data is a Lightweight Java Persistence package](https://github.com/cybersearch2/classy_data). It builds both an Eclipse plug-in and a P2 repository to publish it.
Classy Data is layered on top of the [OrmLite](http://ormlite.com) lightweight Object Relational Mapping (ORM) Java package which also now available as an [Eclipse plug-in](https://bintray.com/cybersearch2/com.j256.ormlite.eclipse). 
It is a client-side persistence framework which adheres to a familiar industry specification and is suitable for resource-constrained platforms such as Android. 
Classy Data implements version 1 of the <a href="http://docs.oracle.com/javaee/6/api/javax/persistence/package-summary.html">javax.persistence</a> 
package which delivers a lean package, but sufficient for client-side persistence. JPA features include persistence.xml configuration, an entity manager factory, 
named queries and transactions which automatically roll back if an exception occurs. For more details, refer to [Lightweight JPA in a nutshell](http://cybersearch2.com.au/develop/jpa_intro.html)></a>

   
The Classy Data Eclipse Plug-in uses the [h2 database](http://www.h2database.com/html/main.html) as this is both supported by OrmLite and is available as a plug-in in the Orbit P2 repository.

## Usage of ORMLite in Classy Data

Although Classy Data places a JPA application layer on top of ORMLite, it does not hide it because all queries are based on ORMLite's SQL query "compiler",
which provides comprehensive support for programmatically generating SQL queries. Therefore, ORMLite Eclipse Plug-in is automatically a dependency where
ever Classy Data is a dependency. Here is an actual code snippet where an ORMLite QueryBuilder object is modified by appending a where clause to select
an object by primary key named "id":

    // build a query with the WHERE clause set to 'id = ?'
    queryBuilder.where().eq("id", primaryKeyArg);

Another ORMLite exposure is through the EntityManager getDelegate() method which returns a helper object to provide access to ORMLite Database
Access Objects (DAOs). Here is the helper's signature:

    /**
     * Returns ORMLite DAO for specified entity class
     * @param clazz  Entity class
     * @return PersistenceDao
     * @throws UnsupportedOperationException if class is unknown for this persistence unit
     */
    public PersistenceDao<?, ?> getDaoForClass(Class<?> clazz)
    {
    ...
    }
    
This allows customization where the JPA implementation is not sufficient.

## Build Instructions

The build project is configured to work with the latest Classy Data version at time of creation, which is version 2.3.0. There are no other build artifacts apart from the P2 repository.

### Prequisites

* Java 8 
* [Maven](https://maven.apache.org/download.cgi) - v3.5.2+ recommended
* [Git](https://git-scm.com/downloads)
* Set up Maven toolchains configuration to point to your Java 8 JDK installation. 
Refer [Guide to Using Toolchains](https://maven.apache.org/guides/mini/guide-using-toolchains.html) and [JDK Toolchain](http://maven.apache.org/plugins/maven-toolchains-plugin/toolchains/jdk.html)

You should also ensure your Git [username](https://help.github.com/articles/setting-your-username-in-git/) and 
[commit email address](https://help.github.com/articles/setting-your-commit-email-address-in-git/) are configured.

### Steps

1. Clone this repository to your workspace and go to the newly created "au.com.cybersearch2.classy_data" project sub directory. 
1. From a command terminal, launch a Maven build using parameters you will find in the provided build.sh file. If on Linux, you can run build.sh as a shell command.
1. If a first time build, you will potentially observe a large number of files being downloaded to be cached on the local Maven repository. If so, it may be time for a coffee break.

The build, when successful, creates a P2 repository in project sub directory releng/classy_data.update/target/repository.


### Build Project Details

The build project is created guided by the Vogella [Eclipse Tycho for building plug-ins, OSGi bundles and Eclipse applications - Tutorial](http://www.vogella.com/tutorials/EclipseTycho/article.html).
Tycho is a Maven plug-in library specifically for building Eclipse products. It has a highly engineered approach which requires a lot of project structure, even for a modest plug-in.

The project structure is:

* / (root) Top- level POM declares group id, artifact id, version and some project information.  Note the SNAPSHOT version convention is always used, even for releases.
* /bundle/classy_data.plugin The actual plug-in project. Note the sources are not included in the distribution, but instead are downloaded and unpacked from the Maven Central repository.
* /feature/au.com.cybersearch2.classy_data.feature The feature definition which provides information needed to install the plug-in, including the license text.
* /releng/classy_data.configuration Configures Tycho, Toolchains and software versions
* /releng/classy_data.target Contains the target configuration which identifies which features are to be made available for building the plugin. Ensures build consistency over time.
* /releng/classy_data.update The sub project which builds the P2 repository. The plug-in artifacts are given a unique identity, base on a timestamp, for each build.

As the sources are downloaded from the Maven Central repository, it ensures that a release version of Classy Data is being built, and not a snapshot, as happens when sources come from GitHub.
 
### Trouble Shooting

If the expected "Build successful" message does not appear at the end of the build, you must try to understand the error information provided by Maven. Generally, things to check:

1. All required command parameters correctly entered (refer build.sh file in root location).
1. The machine has internet access, there are no internet outages and, if required, the http proxy are details configured in Maven settings file.
1. Check the toolchains.xml file, co-located with the Maven settings file, is correctly configured. Note that all JDK Toolchain items must be correct even though only one is used at any time.
1. If the Java compile build stage fails, delete the contents of directories /bundle/ormlite.plugin/src and /bundle/ormlite.plugin/libs and start the build again. This checks for a file corrupted on download/unpack.


