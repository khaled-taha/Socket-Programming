# Socket-Programming
Example of Socket Programming with java (Client and Serever)

```java
import java.io.*;
import java.net.Socket;
import java.util.Scanner;

public class Client {

    String hostName;
    int port;
    Socket socket = null;
    InputStreamReader inputStreamReader = null;
    OutputStreamWriter outputStreamWriter = null;
    BufferedReader bufferedReader = null;
    PrintWriter printWriter = null;

    public Client(String hostName, int port) {
        Start(hostName, port);
    }
    public void Start(String hostName, int port) {

        try {
            socket = new Socket(hostName, port);
            inputStreamReader = new InputStreamReader(socket.getInputStream());
            outputStreamWriter = new OutputStreamWriter(socket.getOutputStream());
            bufferedReader = new BufferedReader(inputStreamReader);
            printWriter = new PrintWriter(outputStreamWriter, true);

            System.out.println(bufferedReader.readLine());
            Scanner scanner = new Scanner(System.in);
            while (true) {
                System.out.print("Client : ");
                String msgToSend = scanner.nextLine();
                printWriter.println(msgToSend);
                if (msgToSend.equalsIgnoreCase("BYE")) {
                    break;
                }
                System.out.println(bufferedReader.readLine());
            }
        } catch (IOException ex) {
           System.out.println(ex.getMessage());
        } finally {
            try {
                stop();
            } catch (IOException ex) {
               System.out.println(ex.getMessage());
            }
        }
    }

    public void stop() throws IOException {
        socket.close();
        inputStreamReader.close();
        outputStreamWriter.close();
        bufferedReader.close();
        printWriter.close();
    }
 
}
```
******************************************************************************************************************
```java
import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;

public class Serever {

    int port;
    Socket socket = null;
    InputStreamReader inputStreamReader = null;
    OutputStreamWriter outputStreamWriter = null;
    BufferedReader bufferedReader = null;
    PrintWriter printWriter = null;
    ServerSocket serversocket = null;

    public Serever(int port) {
        Start(port);
    }

    public void Start(int port) {

        try {
            serversocket = new ServerSocket(port);
        } catch (IOException ex) {
            System.out.println(ex.getMessage());
        }

            try {
                System.out.println("erhrer");
                socket = serversocket.accept();
                System.out.println("Client Connected");
                inputStreamReader = new InputStreamReader(socket.getInputStream());
                outputStreamWriter = new OutputStreamWriter(socket.getOutputStream());
                bufferedReader = new BufferedReader(inputStreamReader);
                printWriter = new PrintWriter(outputStreamWriter, true);
                printWriter.println("server: Hi Client");
                
                while (true) {
                    String msgFromClient = bufferedReader.readLine();
                    if (msgFromClient.equalsIgnoreCase("BYE")) {
                        System.out.println("Client Disconnected");
                        break;
                    }
                    printWriter.println("server: " + msgFromClient);
                }

            } catch (IOException ex) {
                System.out.println(ex.getMessage());
            } finally {
                try {
                    stop();
                } catch (IOException ex) {
                    System.out.println(ex.getMessage());
                }
            }
    }

    public void stop() throws IOException {
        socket.close();
        inputStreamReader.close();
        outputStreamWriter.close();
        bufferedReader.close();
        printWriter.close();
    }
}
```
****************************************************************************************************
```java
public class SocketProgramming {

    public static void main(String[] args) {

        new Thread(new Runnable() {
            @Override
            public void run() {
                Serever serever = new Serever(5000);
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                Client client = new Client("localhost", 5000);
            }
        }).start();

    }
}
```
