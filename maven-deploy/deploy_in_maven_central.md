# How to deploy to maven central
#### Ceu Biolab

Before starting with these steps, it is important to create a namespace in Maven Central Repository.
https://central.sonatype.com

### GPG

1. Install GnuPG for Linux, or WinGPG for Windows.

2. To generate a key, run **gpg --gen-key**.  
   This key is a cryptographic key used for signing and encrypting information. Maven Central requires the key to ensure that the artifacts come from a trusted source and have not been tampered with furing transit.

3. To see the key, run **gpg --list-keys**.  

   pub   rsa4096 2024-10-04 [SC] [*expiration date*]
      *HERE THE KEY*
uid        [  absoluta ] *ceu.biolab* (.) <*ceubiolab@gmail.com*>
sub   rsa4096 2024-10-04 [E] [*expiration date*]
4. Other people need your public key to verify the files, so you have to distribute the key to a key server by running **gpg --keyserver keyserver.ubuntu.com --send-keys YOUR_KEY**.  
Use an **external network** to do this. Using university Wifi will not work because the ports are **closed**.

### settings.xml configuration
In your .m2 folder, create a settings.xml file with the structure:

```
<settings>
  <servers>
    <server>
      <id>central</id>
      <username>ASK_CEU_BIOLAB_FOR_CREDENTIALS</username>
      <password>ASK_CEU_BIOLAB_FOR_CREDENTIALS</password>
      <configuration>
            <gpg.keyname>PUBLIC_KEY</gpg.keyname>
        </configuration>
    </server>
  </servers>
</settings>
```
### pom.xml configuration
Add this line to provide the location of your source code:
```
    <scm>
        <connection>scm:git:git://github.com/LOCATION/REPOSITORY.git</connection>
        <developerConnection>scm:git:ssh://github.com/LOCATION/REPOSITORY.git</developerConnection>
        <url>LINK_TO_REPOSITORY</url>
    </scm>
```
Also add the following plugins:
```
    <plugin>
        <groupId>org.sonatype.central</groupId>
        <artifactId>central-publishing-maven-plugin</artifactId>
        <version>0.6.0</version>                
        <extensions>true</extensions>
        <configuration>
            <publishingServerId>central</publishingServerId>
        </configuration>
    </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-source-plugin</artifactId>
        <version>3.3.1</version>
        <executions>
            <execution>
                <id>attach-sources</id>
                <phase>package</phase>
                <goals>
                    <goal>jar</goal>
                </goals>
            </execution>
        </executions>
    </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-gpg-plugin</artifactId>
        <version>3.1.0</version>
        <executions>
            <execution>
                <id>sign-artifacts</id>
                <phase>package</phase>
                <goals>
                    <goal>sign</goal>
                </goals>
            </execution>
        </executions>
    </plugin>
```
**NOTE:** The *publishingServerId* in the *central-publishing-maven-plugin* **MUST** be the same as *id* in the *settings.xml*. 
In this example it is *central*.

### Deploy and publish
To deploy to Maven Central Repository run **mvn clean deploy -Dgpg.skip=false** and wait for it to appear in https://central.sonatype.com/publishing/deployments.

To publish, click on the *Publish* botton to the right of the Deployment and wait, as it takes some time.

To test if it has been published just import it to another project.
