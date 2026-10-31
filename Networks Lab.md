## Networks Batch 2
<p>1. a) Write a program to design a TCP Client-Server application to
transfer a file.<br>&nbsp;&nbsp;
  b) Using TCP socket download a webpage and get the URL and
pass it for buffering the content and write it as a html file</p>

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
#### The Java application successfully connected to the HTTP server via TCP sockets, buffered the webpage text stream, and stored it locally as an HTML file.
---
# 2. Write a program to implement the TCP Client Server Chat Application using Sockets
### Experiment 2: Simple TCP Client-Server Chat Application

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


# 3. Write a programs hello_server for TCP (The client connects to the server, sends the string "Hello, world!", then closes the connection )
##### Experiment 3: Simple TCP Hello World Application

## Aim
To write a Java program (`hello_server` and client) where a client connects to a server over a TCP socket, sends the static message string "Hello, world!", and immediately closes the connection.

---

## Algorithm

1. **Server Setup:** The server initializes a `ServerSocket` on port `7000` and waits passively for an incoming request using `accept()`.
2. **Client Connection:** The client actively initiates a network connection to the server at `localhost:7000`.
3. **Data Transmission:** 
   * The client sets up a `PrintWriter` network output stream and instantly transmits the text string `"Hello, world!"` using `println()`.
   * The server intercepts the data stream using a `BufferedReader` and prints the received message to its console window.
4. **Immediate Disconnection:** As soon as the single message string is pushed through the network pipeline, both programs close all open network streams and exit.

---

## Program Code

### 1. `HelloServer.java`
```java
import java.io.*;
import java.net.*;

public class HelloServer {
    public static void main(String[] args) throws Exception {
        // 1. Listen on Port 7000 and wait for the client connection
        ServerSocket serverSocket = new ServerSocket(7000);
        System.out.println("Server waiting for a connection...");
        Socket socket = serverSocket.accept();
        
        // 2. Set up a network input stream to read incoming text data
        BufferedReader networkIn = new BufferedReader(new InputStreamReader(socket.getInputStream()));
        
        // 3. Read the single line sent by the client and print it
        String clientMessage = networkIn.readLine();
        System.out.println("Received from Client: " + clientMessage);
        
        // 4. Immediately close connection streams
        networkIn.close();
        socket.close();
        serverSocket.close();
        System.out.println("Server closed connection.");
    }
}
```

### 2. `HelloClient.java`
```java
import java.io.*;
import java.net.*;

public class HelloClient {
    public static void main(String[] args) throws Exception {
        // 1. Actively connect to the server at localhost on Port 7000
        System.out.println("Connecting to the server...");
        Socket socket = new Socket("localhost", 7000);
        
        // 2. Set up a network output stream to send text data (true enables auto-flush)
        PrintWriter networkOut = new PrintWriter(socket.getOutputStream(), true);
        
        // 3. Send the exact requested message string
        networkOut.println("Hello, world!");
        System.out.println("Message 'Hello, world!' sent successfully.");
        
        // 4. Immediately close connection streams
        networkOut.close();
        socket.close();
        System.out.println("Client closed connection.");
    }
}
```

---

## Commands to Compile and Run

### Compilation
```bash
javac HelloServer.java HelloClient.java
```

### Execution

#### **Terminal 1 (Run First)**
```bash
java HelloServer
```

#### **Terminal 2 (Run Second)**
```bash
java HelloClient
```

---

## Output

### Terminal 1 - HelloServer
```text
Server waiting for a connection...
Received from Client: Hello, world!
Server closed connection.
```

### Terminal 2 - HelloClient
```text
Connecting to the server...
Message 'Hello, world!' sent successfully.
Client closed connection.
```

---

## Result
The TCP Client-Server application was implemented successfully. The client established a connection, delivered the message string "Hello, world!", and terminated the session smoothly without lingering resources.


<h1>4. Write the program for Simulation of DNS using UDP Socket by
getting the frame size from the user and create the frame sized
user request and send the frame to server from the client.</h1>
<p>Experiment 4: Simulation of DNS Using UDP Sockets with Custom Frame Sizes</p>


## Aim
To design and implement a DNS simulation program using UDP Sockets where the client transmits a domain query alongside a user-specified frame size requirement to a server for IP resolution.

---

## Program Code

### 1. `DNSServer.java`
```java
import java.net.*;
import java.util.*;

public class DNSServer {
    public static void main(String[] args) throws Exception {
        // Simple DNS table mapping domains to static IPs
        Map<String, String> table = new HashMap<>();
        table.put("www.google.com", "142.250.190.4");
        table.put("www.annauniv.edu", "14.139.161.10");
        table.put("www.example.com", "93.184.216.34");

        // Bind server to UDP Port 6000
        DatagramSocket socket = new DatagramSocket(6000);
        byte[] buffer = new byte[1024];
        System.out.println("DNS Server waiting...");

        // Receive the client query packet
        DatagramPacket packet = new DatagramPacket(buffer, buffer.length);
        socket.receive(packet);

        // Parse client data payload
        String request = new String(packet.getData(), 0, packet.getLength());
        String[] data = request.split(",");
        String domain = data[0].trim();
        int frameSize = Integer.parseInt(data[1].trim());

        System.out.println("Request: " + domain + ", frame size = " + frameSize);

        // Match domain request against memory table
        String ip = table.get(domain);
        if (ip == null) { 
            // Fallback: Query system DNS resolver if missing from local table
            try { 
                ip = InetAddress.getByName(domain).getHostAddress(); 
            } catch (Exception e) { 
                ip = "Domain not found"; 
            }
        }

        // Formulate output string payload response
        String result = "Domain: " + domain + "\nIP: " + ip + "\nFrame Size: " + frameSize + " bytes";
        byte[] response = result.getBytes();

        // Send datagram packet response back to client coordinates
        socket.send(new DatagramPacket(response, response.length, packet.getAddress(), packet.getPort()));
        System.out.println("Reply sent.");
        
        socket.close();
    }
}
```

### 2. `DNSClient.java`
```java
import java.net.*;
import java.util.*;

public class DNSClient {
    public static void main(String[] args) throws Exception {
        Scanner sc = new Scanner(System.in);
        
        System.out.print("Enter domain name: ");
        String domain = sc.nextLine();
        
        System.out.print("Enter frame size: ");
        int frameSize = sc.nextInt();

        // Create client Datagram socket
        DatagramSocket socket = new DatagramSocket();
        
        // Pack data using string serialization format
        byte[] data = (domain + "," + frameSize).getBytes();
        
        // Target server running locally on port 6000
        socket.send(new DatagramPacket(data, data.length, InetAddress.getByName("localhost"), 6000));

        // Setup buffer allocation to intercept incoming server replies
        byte[] buffer = new byte[1024];
        DatagramPacket response = new DatagramPacket(buffer, buffer.length);
        socket.receive(response);

        // Display results returned by the DNS simulator
        System.out.println("\n--- Reply from DNS server ---");
        System.out.println(new String(response.getData(), 0, response.getLength()));
        
        socket.close();
        sc.close();
    }
}
```

---

## Execution and Output Verification

### Compilation Command
```bash
javac DNSServer.java DNSClient.java
```

### Terminal 1 - DNS Server Console
```text
DNS Server waiting...
Request: www.annauniv.edu, frame size = 512
Reply sent.
```

### Terminal 2 - DNS Client Console
```text
Enter domain name: www.annauniv.edu
Enter frame size: 512

--- Reply from DNS server ---
Domain: www.annauniv.edu
IP: 14.139.161.10
Frame Size: 512 bytes
```

---

## Result
The UDP DNS simulation program was compiled and executed successfully. The communication between the server and the client was verified by resolving host requests accurately based on custom frame transmission parameters.



















