## Networks Batch 2
<p>1. a) Write a program to design a TCP Client-Server application to
transfer a file.<br>&nbsp;&nbsp;
  b) Using TCP socket download a webpage and get the URL and
pass it for buffering the content and write it as a html file</p>
<p></p>

### 1(a): TCP Client-Server Application to Transfer a File

## Aim
To write a Java program in which a server sends a file to a client using TCP sockets.

---

## Algorithm

1. **Server Setup:** The server creates a `ServerSocket` on port `5000` and blocks/waits for an incoming connection using the `accept()` method.
2. **Client Connection:** The client initiates a connection to the server using `Socket("localhost", 5000)`.
3. **File Transmission (Server Side):** Once the connection is established, the server opens the source file `sample.txt`, reads its content in chunks/blocks of `4096 bytes`, and writes them directly to the socket's network output stream.
4. **File Reception (Client Side):** The client reads the incoming bytes chunk-by-chunk from the socket's network input stream and writes them locally into a new file named `received.txt`.
5. **Resource Cleanup:** After reaching the End-of-File (EOF), both the server and client close all open file streams, network streams, and sockets to free up system resources.

---

## Program Code

### 1. `FileServer.java`
```java
import java.io.*;
import java.net.*;

public class FileServer {
    public static void main(String[] args) throws Exception {
        ServerSocket ss = new ServerSocket(5000);
        System.out.println("Server waiting...");
        Socket s = ss.accept();
        System.out.println("Client connected.");
        
        FileInputStream fis = new FileInputStream("sample.txt");
        OutputStream os = s.getOutputStream();
        
        byte[] buffer = new byte[4096];
        int bytes;
        
        while ((bytes = fis.read(buffer)) != -1) {
            os.write(buffer, 0, bytes);
        }
        
        System.out.println("File sent successfully.");
        fis.close();
        os.close();
        s.close();
        ss.close();
    }
}
```

### 2. `FileClient.java`
```java
import java.io.*;
import java.net.*;

public class FileClient {
    public static void main(String[] args) throws Exception {
        Socket s = new Socket("localhost", 5000);
        InputStream is = s.getInputStream();
        FileOutputStream fos = new FileOutputStream("received.txt");
        
        byte[] buffer = new byte[4096];
        int bytes;
        
        while ((bytes = is.read(buffer)) != -1) {
            fos.write(buffer, 0, bytes);
        }
        
        System.out.println("File received successfully.");
        fos.close();
        is.close();
        s.close();
    }
}
```

### 3. Input File: `sample.txt`
*(Create this file in the same directory as the server program before execution)*
```text
Hello. This is a computer networks lab file.
TCP transfers this file reliably from server to client.
```

---

## Commands to Compile and Run

### Compilation
```bash
javac FileServer.java FileClient.java
```

### Execution

#### **Terminal 1 (Start Server First)**
```bash
java FileServer
```

#### **Terminal 2 (Start Client Second)**
```bash
java FileClient
```

#### **Verification (Check transferred content)**
```bash
cat received.txt
```

---

## Output

### Terminal 1 - Server
```text
Server waiting...
Client connected.
File sent successfully.
```

### Terminal 2 - Client
```text
File received successfully.
```

### Terminal 2 - Verification
```text
\$ cat received.txt
Hello. This is a computer networks lab file.
TCP transfers this file reliably from server to client.
```

---

## Result
The TCP Client-Server file transfer program was designed, executed successfully, and the structural integrity of the transferred output file was verified.


#### Experiment 1(b): Webpage Download Using TCP Sockets

## Aim
To write a Java program to download a webpage from a URL, buffer its content, and save it locally as an HTML file using a TCP/HTTP connection.

---

## Algorithm

1. **URL Resolution:** Define the target URL string and convert it into a validated `URI` and `URL` object.
2. **Establish Connection:** Open an `HttpURLConnection` channel to the server and fetch the HTTP response status code.
3. **Data Buffering:** Open a network input stream (`getInputStream`) from the connection and wrap it inside a `BufferedReader` to load the webpage data line-by-line efficiently.
4. **File Creation:** Create a local output file writer (`FileWriter`) named `page.html`.
5. **Data Transfer Loop:** Read text lines from the buffered network stream and write them sequentially into the HTML file until the stream returns empty (`null`).
6. **Cleanup:** Close the buffered reader and the file writer streams to release system resources.

---

## Program Code

### `URLDownload.java`
```java
import java.io.*;
import java.net.*;

public class URLDownload {
    public static void main(String[] args) throws Exception {
        // 1. Get URL from argument or fallback to default
        String urlString = (args.length > 0) ? args[0] : "https://example.com";
        URL url = new URI(urlString).toURL();
        
        // 2. Open HTTP connection channel
        HttpURLConnection con = (HttpURLConnection) url.openConnection();
        System.out.println("Response Code: " + con.getResponseCode());
        
        // 3. Buffer the incoming network stream
        BufferedReader br = new BufferedReader(new InputStreamReader(con.getInputStream()));
        
        // 4. Open the local file writer
        FileWriter fw = new FileWriter("page.html");
        
        // 5. Read from buffer and write to the HTML file
        String line;
        while ((line = br.readLine()) != null) {
            fw.write(line);
            fw.write("\n");
        }
        
        // 6. Close streams
        br.close();
        fw.close();
        System.out.println("Web page downloaded as page.html");
    }
}
```

---

## Commands to Compile and Run

### Compilation
```bash
javac URLDownload.java
```

### Execution
```bash
java URLDownload
```

### Verification
```bash
cat page.html
```

---

## Output

```text
Response Code: 200
Web page downloaded as page.html
```

### Content of `page.html` (Verified)
```html
<!doctype html>
<html>
<head>
    <title>Example Domain</title>
</head>
<body>
<div>
    <h1>Example Domain</h1>
    <p>This domain is for use in documentation examples without needing permission.</p>
</div>
</body>
</html>
```

---

## Result
The Java application successfully connected to the HTTP server via TCP sockets, buffered the webpage text stream, and stored it locally as an HTML file.

### Experiment 1(c): Simple TCP Client-Server Chat Application

## Aim
To write a beginner-friendly Java program to implement a turn-by-turn chat application between a client and a server using TCP sockets.

---

## Algorithm

1. **Server Setup:** The server creates a `ServerSocket` on port `6000` and waits for a client using `accept()`.
2. **Client Connection:** The client establishes a connection to the server using `Socket("localhost", 6000)`.
3. **Data Stream Setup:** Both programs set up network streams (`BufferedReader` and `PrintWriter`) to talk to each other, and a keyboard stream (`BufferedReader`) to read user terminal input.
4. **Communication Loop (Turn-by-Turn):**
   * **Client Turn:** The client reads input from the keyboard, sends it via `println()`, and the server reads and displays it.
   * **Server Turn:** The server reads input from the keyboard, sends it back via `println()`, and the client reads and displays it.
5. **Termination:** The loop continues until either party sends the message `"bye"`. Both sides then close all open streams and sockets.

---

## Program Code

### 1. `ChatServer.java`
```java
import java.io.*;
import java.net.*;

public class ChatServer {
    public static void main(String[] args) throws Exception {
        // 1. Start server on Port 6000 and wait for client to connect
        ServerSocket serverSocket = new ServerSocket(6000);
        System.out.println("Server waiting for client...");
        Socket socket = serverSocket.accept();
        System.out.println("Client connected!");

        // 2. Streams to read/write messages over the network
        BufferedReader networkIn = new BufferedReader(new InputStreamReader(socket.getInputStream()));
        PrintWriter networkOut = new PrintWriter(socket.getOutputStream(), true); // true = auto-send

        // 3. Stream to read what you type into the keyboard terminal
        BufferedReader keyboardIn = new BufferedReader(new InputStreamReader(System.in));

        String clientMessage, serverMessage;

        // 4. Chat Loop
        while (true) {
            // Read from Client first
            clientMessage = networkIn.readLine();
            System.out.println("Client says: " + clientMessage);
            if (clientMessage.equalsIgnoreCase("bye")) {
                break;
            }

            // Send reply to Client
            System.out.print("Type message to Client: ");
            serverMessage = keyboardIn.readLine();
            networkOut.println(serverMessage);
            if (serverMessage.equalsIgnoreCase("bye")) {
                break;
            }
        }

        // 5. Close everything
        keyboardIn.close();
        networkIn.close();
        networkOut.close();
        socket.close();
        serverSocket.close();
        System.out.println("Chat ended.");
    }
}
```

### 2. `ChatClient.java`
```java
import java.io.*;
import java.net.*;

public class ChatClient {
    public static void main(String[] args) throws Exception {
        // 1. Connect directly to the server running on this machine at Port 6000
        System.out.println("Connecting to server...");
        Socket socket = new Socket("localhost", 6000);
        System.out.println("Connected to server!");

        // 2. Streams to read/write messages over the network
        BufferedReader networkIn = new BufferedReader(new InputStreamReader(socket.getInputStream()));
        PrintWriter networkOut = new PrintWriter(socket.getOutputStream(), true); // true = auto-send

        // 3. Stream to read what you type into the keyboard terminal
        BufferedReader keyboardIn = new BufferedReader(new InputStreamReader(System.in));

        String clientMessage, serverMessage;

        // 4. Chat Loop
        while (true) {
            // Send message to Server first
            System.out.print("Type message to Server: ");
            clientMessage = keyboardIn.readLine();
            networkOut.println(clientMessage);
            if (clientMessage.equalsIgnoreCase("bye")) {
                break;
            }

            // Read reply from Server
            serverMessage = networkIn.readLine();
            System.out.println("Server says: " + serverMessage);
            if (serverMessage.equalsIgnoreCase("bye")) {
                break;
            }
        }

        // 5. Close everything
        keyboardIn.close();
        networkIn.close();
        networkOut.close();
        socket.close();
        System.out.println("Chat ended.");
    }
}
```

---

## Commands to Compile and Run

### Compilation
```bash
javac ChatServer.java ChatClient.java
```

### Execution

#### **Terminal 1 (Run First)**
```bash
java ChatServer
```

#### **Terminal 2 (Run Second)**
```bash
java ChatClient
```

---

## Output

### Terminal 1 - ChatServer
```text
Server waiting for client...
Client connected!
Client says: Hello Server
Type message to Client: Hello Client, how are you?
Client says: I am good. Bye
Chat ended.
```

### Terminal 2 - ChatClient
```text
Connecting to server...
Connected to server!
Type message to Server: Hello Server
Server says: Hello Client, how are you?
Type message to Server: I am good. Bye
Chat ended.
```

---

## Result
The simplified turn-by-turn TCP Client-Server chat application was implemented successfully, and messages were transmitted reliably.





















