## Networks Batch 2
<p>1. a) Write a program to design a TCP Client-Server application to
transfer a file.<br>&nbsp;&nbsp;
  b) Using TCP socket download a webpage and get the URL and
pass it for buffering the content and write it as a html file</p>
<p></p>

## Part A: TCP File Transfer

### 1. Server Code (`FileServer.java`)
```java
import java.io.*;
import java.net.*;

public class FileServer {
    public static void main(String[] args) throws Exception {
        ServerSocket server = new ServerSocket(5001);
        System.out.println("Server waiting...");
        Socket socket = server.accept();
        
        DataInputStream in = new DataInputStream(socket.getInputStream());
        FileOutputStream fos = new FileOutputStream("received.txt");
        
        // Size explicitly defined to support older Java compilers
        byte[] buffer = new byte[4096];
        int bytesRead;
        
        while ((bytesRead = in.read(buffer)) != -1) {
            fos.write(buffer, 0, bytesRead);
        }
        
        System.out.println("File received successfully!");
        fos.close();
        socket.close();
        server.close();
    }
}
```

### 2. Client Code (`FileClient.java`)
```java
import java.io.*;
import java.net.*;

public class FileClient {
    public static void main(String[] args) throws Exception {
        Socket socket = new Socket("localhost", 5001);
        
        File file = new File("source.txt");
        FileInputStream fis = new FileInputStream(file);
        DataOutputStream out = new DataOutputStream(socket.getOutputStream());
        
        byte[] buffer = new byte[4096];
        int bytesRead;
        
        while ((bytesRead = fis.read(buffer)) != -1) {
            out.write(buffer, 0, bytesRead);
        }
        
        System.out.println("File sent successfully!");
        fis.close();
        out.close();
        socket.close();
    }
}
```

---

## Part B: Webpage Downloader (`WebDownloader.java`)

```java
import java.io.*;
import java.net.*;

public class WebDownloader {
    public static void main(String[] args) throws Exception {
        String urlString = "http://example.com";
        URL url = new URL(urlString);
        
        // Works on all versions using basic Port 80
        Socket socket = new Socket(url.getHost(), 80);
        
        PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
        BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
        PrintWriter fileOut = new PrintWriter(new FileWriter("downloaded.html"));
        
        // Raw HTTP format compatible with old and new web layouts
        out.println("GET " + url.getPath() + " HTTP/1.1");
        out.println("Host: " + url.getHost());
        out.println("Connection: close");
        out.println(); 
        
        String line;
        boolean isHeader = true;
        
        while ((line = in.readLine()) != null) {
            if (isHeader) {
                if (line.isEmpty()) isHeader = false; 
                continue; 
            }
            fileOut.println(line); 
        }
        
        System.out.println("Webpage downloaded to downloaded.html!");
        fileOut.close();
        in.close();
        socket.close();
    }
}
```


## 📋 Execution & Lab Checklist

### Part A (File Transfer)
1. Open Terminal 1 and run the Server:
   ```bash
   javac FileServer.java
   java FileServer
   ```
2. Open Terminal 2, create a sample file, and run the Client:
   ```bash
   echo "Exam Lab Test Content" > source.txt
   javac FileClient.java
   java FileClient
   ```

### Part B (Webpage Downloader)
1. Run in any terminal:
   ```bash
   javac WebDownloader.java
   java WebDownloader
   ```
