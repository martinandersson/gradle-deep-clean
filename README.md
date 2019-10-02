# Gradle Deep Clean

Super simple Java script (yes, two separate words) that invokes `gradle clean`
recursively (or `./gradlew clean` if the wrapper is present).

The script (which
[is a file called "gdc"](https://github.com/martinanderssondotcom/gradle-deep-clean/blob/master/gdc)
) is useful to clean a whole directory tree out of build artifacts and
other crap. For example, to reduce space used by synced cloud storage and
consequently Internet bandwith used (which was author's one and only intention
with this small piece of software).

**A Java runtime is required** (at least version 11).

The script should work on Windows and macOS but this has not been tested. The
only OS tested is Ubuntu Budgie 19.04.

## Run

The examples in this section assume that the script is available and has been
downloaded:

    wget https://github.com/martinanderssondotcom/gradle-deep-clean/raw/master/gdc

Furthermore, it is also assumed that the current working directory is the
directory where to start the recursive search for Gradle projects to clean. A
different directory can be set using the `-d` option (see [Options](#options)).

### Using Java

    java --source 11 gdc

An explicit compilation step is not necessary. See Oracle's
[`java` docs](https://docs.oracle.com/en/java/javase/12/tools/java.html#GUID-3B1CE181-CD30-4178-9602-230B800D4FAE)
and
[JEP 330](https://openjdk.java.net/jeps/330).

### As a local executable file

    ./gdc

This is possible because
[the first line in the script](https://github.com/martinanderssondotcom/gradle-deep-clean/blob/master/gdc#L1)
is a shebang directive asking `/usr/bin/env` to run the very same command
provided in the previous example.

### From user's path

First, we investigate what secret folders are automagically picked up by the
environment:

    cat ~/.profile

..on my machine I see this:

    # set PATH so it includes user's private bin if it exists  
    if [ -d "$HOME/bin" ] ; then  
        PATH="$HOME/bin:$PATH"  
    fi

So all I need to do is to create the "bin" folder, put the script inside and
make it executable:

    mkdir ~/bin
    URL=https://github.com/martinanderssondotcom/gradle-deep-clean/raw/master/gdc
    wget $URL -O ~/bin/gdc
    chmod +x ~/bin/gdc

In order to reload the environment we login and logout. Then the script should
be able to run from any directory and without using the "./" prefix:

    gdc

## Options

### Directory

There's only one option currently supported; `-d` or `--dir` as in "directory".
I.e., where do we start our search for Gradle projects to clean? If this option
is left out, the working directory is assumed.

Spaces are allowed and does not need to be quoted. For example, search
recursively starting in "/my projects":

    ./gdc -d /my projects

Or search the entire drive:

    ./gdc -d /

These entities are not searched but instead silently ignored:

* Links.
* Hidden folders <sup>1</sup>.
* Any folder named "node_modules".
* Subdirectories of an already identified Gradle project <sup>2</sup>.

Ignored but logged with a warning message:

* Folders for which the script was denied access.

[^]: blabla

<sub><sup>1</sup> Java <13 on Windows will include hidden folders in the search
(see this [bug](https://bugs.openjdk.java.net/browse/JDK-8215467)).</sub>

<sub><sup>2</sup> In this case it is assumed that the Gradle executable will
clean subprojects if subprojects are defined.</sub>

## Editing the file (as of 2019-09-16)

One would think that opening a Java file standalone (outside of a pre-defined
project) in a Java editor with syntax highlighting, code completion and so
forth, should be just as easy and intuitive as it is to open an image file
with an image editor. Alas that is not the case.

What follows is my notes on how to get NetBeans and IntelliJ to work for the
simple task of editing the `gdc` script file (or any Java script file for that
matter).

### Using NetBeans

NetBeans can open a single file and even associate unknown file extensions with
a manually provided file type/MIME (Tools -> Options -> Miscellaneous -> Files).

But in order to do so there has to be a file extension to begin with, which
the file `gdc` does not have.

The only workaround I have found is to temporarily rename the file to "gdc.java"
and then open it in NetBeans.

### Using IntelliJ

There's no easy way around this. We have to create a project manually and then
hack IntelliJ a bit in order to be able to properly handle a Java source code
file without a file extension.

#### Create a new project

* Click on the button "Create New Project" or select "File -> New -> Project" in
the menu.
* In the dialog that opens up; project type "Java" should already be ticked.
Also make sure there's a Java project SDK selected in the drop-down.
* Hit "next" two times in a row.
* Pick a fancy name and specify the project location as the folder where you
  cloned this repository. Hit "Finish".
  * If IntelliJ asks to "overwrite" stuff then just hit "Yes" (nothing is
    overwritten, IntelliJ is being a retard).

#### Move sources folder from src/ to project root

* Go to the menu "File" -> "Project Structure".
* In the new dialog, navigate to "Project Settings" -> "Modules". Select tab
  "Sources".
* Click the "x" next to the "src" folder in order to "unmark" it as a source
  folder. Then physically delete said folder from your disk (we won't be using
  it).
* Right-click on the root project folder and click "Sources". This will mark the
  root as a source folder.
* Exit out from the dialog (hit "OK").

#### Create a new Java association

* Find the "gdc" file in your project explorer.
* Right-click on it and select "Associate with File Type...".
* In the next dialog that appears, select "Java" and hit "OK".

## Alternatives

The only thing I've found as an alternative is
[rock3r/deep-clean](https://github.com/rock3r/deep-clean) which is a Kotlin
script.

## Disclamer

I feel almost ashamed for breaking my own "document first" principle with this
script file as the source code in it is not documented at all.

To my defense, it is a script file which should be as small and as simple as
possible. I also wanted to absolutely minimize my effort and time put into
writing a script with such a low utility factor and only 1 end user lol.

Any questions, just give me a holla! =)

/ Martin
