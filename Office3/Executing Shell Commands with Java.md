# Executing Shell Commands with Java
we'll take a look at how we can leverage the *Runtime* and *ProcessBuilder* classes to execute shell commands and scripts with Java.

```java
    //Executing a Command from String
    Process process = Runtime.getRuntime().exec("ping www.stackabuse.com");
    printResults(process);
    
    //Specify the Working Directory
      Process process = Runtime.getRuntime()
              .exec("cmd /c dir", null, new File("C:\\Users\\"));
      printResults(process);
```

```java
public static void printResults(Process process) throws IOException {
    BufferedReader reader = new BufferedReader(new InputStreamReader(process.getInputStream()));
    String line = "";
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}
```

Here,













## References
* https://stackabuse.com/executing-shell-commands-with-java/
* [GitHub Project Link]()
