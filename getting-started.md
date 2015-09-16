_First at all, you will need Java JDK 1.6+ and IBM Notes 8.5+ installed in your computer and download the River Framework binaries as a ZIP file from [OpenNTF](http://www.openntf.org/main.nsf/project.xsp?r=project/River%20Framework/releases)._

Let's say that we need to open a IBM Notes session, create a test database, create a document, set a simple value and retrieve it again. Also, say "Hello" :-)

You can download the code from this example from this gist, [GettingStarted.java](https://gist.github.com/mariosotil/997ffc46a330537d0165). Meanwhile, here's how it works:

To begin with, we define an arbitrary name for the test database, and retrieve the password from the command line arguments. 

```java
final String filepath = "river-getting-started.nsf";
String password = args[0];
```

The password needed is the one you use to open IBM Notes. More precisely, the password to open your ID file.

<img alt="IBM Notes login dialog" src="http://www.riverframework.org/images/ibm_notes_login.png"/>

We have to init a Notes thread (an IBM Notes requirement for Java development)

```java
NotesThread.sinitThread();
```

To open an IBM Notes session, we select the wrapper for IBM Notes with the constant `River.LOTUS_DOMINO`. The three next parameters are `server`, `username` and `password`. In the context of IBM Notes, the session will be local when we send only the password. 

```java
Session session = River.getSession(River.LOTUS_DOMINO, null, null, password);
```

Once the session was opened, we try to open the test database called `river-getting-started.nsf`

```java
DefaultDatabase database = session.getDatabase(DefaultDatabase.class, "", filepath);
```

If the database does not exist, when we create it. At first, we select the class to manage the database. In this example, the class selected was `DefaultDatabase`, implemented by the framework. The next parameters are `server` and `filename`. In the context of IBM Notes, the database is local when we send an empty string as server.

```java
if (!database.isOpen()) database = session.createDatabase(DefaultDatabase.class, "", filepath);
```

To create a new document, we can use chaining methods to create the document itself, set a field `Greetings` with the value `Hello World!`, save it and then retrieve its unique ID for River Framework. This is important because this ID is not the same Universal ID from IBM Notes. It has the full path to the object. In this case the ID is made up of the server name, the database file name and the document number. 

```java
String id = database.createDocument()
            .setField("Greetings", "Hello World!")
            .save()
            .getObjectId();
```

To retrieve the document, we use the method `getDocument` from the `database` and retrieve the value of the `Greetings` field as a String.

```java
System.out.println("River Framework says " + database.getDocument(id).getFieldAsString("Greetings"));
```

Finally, we close the session for the IBM Notes wrapper, 

```java
River.closeSession(River.LOTUS_DOMINO);
```

And we finish the Notes thread (once again, an IBM Notes requirement)

```java
NotesThread.stermThread();
```

If you want to run this demo, you will need to do this:

- Un zip the River Framework in some folder
- Download the [GettingStarted.java](https://raw.githubusercontent.com/mariosotil/river-framework-demo/master/GettingStarted/src/main/java/GettingStarted.java) code and put in the same folder.
- Open a Command Prompt (Windows) or Terminal (Linux) and open that folder.
- To compile it, just use this command: 
```
javac -cp .\*;C:\IBM\Notes\jvm\lib\ext\Notes.jar GettingStarted.java
```
- To execute it: 
```
java -cp .;.\*;C:\IBM\Notes\jvm\lib\ext\Notes.jar GettingStarted mypassword
```

Of course, you must change the location of Notes.jar, depending where you installed IBM Notes. Also, use the right 'mypassword' :-)
