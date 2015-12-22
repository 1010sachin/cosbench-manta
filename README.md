[![Build Status](https://travis-ci.org/joyent/cosbench-manta.svg?branch=master)](https://travis-ci.org/joyent/cosbench-manta)

# COSBench Manta Adaptor

This project provides an [adaptor](https://github.com/intel-cloud/cosbench/blob/master/COSBench-Adaptor-Dev-Guide.odt)
for benchmarking [Joyent's object store](https://www.joyent.com/object-storage) [Manta](https://github.com/joyent/manta)
using [COSBench](https://github.com/intel-cloud/cosbench/).

## Requirements
* [COSBench 0.4.1.0](https://github.com/intel-cloud/cosbench/releases/tag/v0.4.1.0)
* [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or higher.
* [Maven 3.3.x](https://maven.apache.org/)

## Building from Source
If you prefer to build from source, you'll also need
[Maven](https://maven.apache.org/), and then invoke:

``` bash
# mvn package
```

This should generate an OSGI bundle inside the `./target` directory that you
can drop into the COSBench `osgi/plugins`` directory.

## Configuration

Sample COSBench job configuration files are available in the `./conf` directory.

## Docker
You can use a preconfigured host with COSBench and the Manta adaptor preinstalled
when you run the project's [Docker](https://www.docker.com/) image.

## Contributions

Contributions welcome! Please ensure that `# mvn checkstyle:checkstyle -Dcheckstyle.skip=false` runs
clean with no warnings or errors.

## Testing

There are no unit tests for this project yet. This would be a great first contribution!

## Releasing the Java Components

In order to release to [Maven central](https://search.maven.org/), you will need [an account] (https://issues.sonatype.org) with [Sonatype OSSRH](http://central.sonatype.org/pages/ossrh-guide.html).
If you do not already have an account, you can click the signup link from the login screen
to begin the process of registering for an account.  After signing up, you will need to add
your sonatype credentials to your your maven settings file.  By default this settings file is
located at `$HOME/.m2/settings.xml`.  In addition to sonatype credentials, you will
also need to add a [gpg signing](https://maven.apache.org/plugins/maven-gpg-plugin/sign-mojo.html) key configuration.

For the security conscious, a [guide to encrypting credentials in maven settings files](https://maven.apache.org/guides/mini/guide-encryption.html) exists to
illustrate how credentials can be protected.

The following is an example settings.xml file:

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                              http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <profiles>
    <profile>
      <id>gpg</id>
      <properties>
        <!-- Customize the following properties to configure your gpg settings. -->
        <gpg.executable>gpg</gpg.executable>
        <gpg.keyname>keyname</gpg.keyname>
        <gpg.passphrase>passphrase</gpg.passphrase>
        <gpg.secretKeyring>${env.HOME}/.gnupg/secring.gpg</gpg.secretKeyring>
      </properties>
    </profile>
  </profiles>
  <servers>
    <server>
      <id>ossrh</id>
      <username>username</username>
      <password>password</password>
    </server>
  </servers>
</settings>
```

To perform a release:

1. Make sure the source builds, test suites pass, and the source and java artifacts can
 be generated and signed:
`mvn clean verify -Prelease`
2. Start from a clean working directory and make sure you have no modified
files in your workspace:
`mvn clean && git status`
3. Prepare the release:
4. `mvn release:clean release:prepare`
4. Enter the version to be associated with this release.
You should be prompted for this version number, and the default assumed version
will be shown and should correspond to the version that was in the pom.xml
file but WITHOUT the `-SNAPSHOT` suffix.
5. Enter the SCM tag to be used to mark this commit in the SCM.
You should be prompted for this tag name, and the default will be
`{projectName}-{releaseVersion}`
6. Enter the new development version.
You should be prompted for this version number, and the default for this will
be an incremented version number of the release followed by a `-SNAPSHOT`
suffix.

 At this point
 * The release plugin will continue and build/package the artifacts.
 * The pom.xml file will be updated to reflect the version change to the release
version.
 * The new pom.xml will be committed.
 * The new commit will be tagged.
 * The pom.xml file will be updated again with the new development version.
 * Then this new pom.xml will be committed.

 If the process fails for some reason during any of these points, you can invoke
`mvn release:rollback` to go back to the preparation point and try again, but
you will also have to revert any SCM commits that were done
(`git reset --hard HEAD^1` command works well for this) as well as remove any
tags that were created (`git tag -l && git tag -d <tagName>` commands help
with this).

7. Push tags to github:
`git push --follow-tags`
In order for the `release:perform` goal to complete successfully, you will need to
push the tags created by the maven release plugin to the remote git server.

8. Perform the actual release:
`mvn release:perform`
A build will be performed and packaged and artifacts deployed to the sonatype
staging repository.

9. Log into the [Sonatype OSSHR Next](https://oss.sonatype.org) web interface
to [verify and promote](http://central.sonatype.org/pages/releasing-the-deployment.html)
the build.

**NOTE**: By default, these instructions assumes the release is being done from a
branch that can be merged into a primary branch upon successful completion,
and that the SCM operations that are carried out by maven plugins will NOT
access the repo, but rather, work on a local copy instead.  The release plugin
as configured in the maven repo sets values for this assumption
(`localCheckout=true` and `pushChanges=false`).

**NOTE**: If the release is being done in a separate fork of the primary
github repo, doing a merge via pull request will not also copy the tags that
were created during the release process.  The tags will have to be created in
the primary repo separately, but this may be preferred anyway.

### Bugs

See <https://github.com/joyent/manta-cosbench/issues>.

## License

The MIT License (MIT)
Copyright (c) 2015 Joyent

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
