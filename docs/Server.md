# Server

In diesem Abschnitt erstellen wir einen Server, zu dem sich ein Client verbinden kann.

## Übersicht

Zum erstellen eines Servers können Sie die Standardbibliotheken von C\# verwenden.

Danach können wir ein Server-Objekt erstellen, das auf einen bestimmten ==Port== hört. 

!!! note
	
	Ein Port wird in der Computernetzwerktechnik verwendet, um den Datenverkehr zwischen verschiedenen Anwendungen oder Diensten zu ermöglichen. Ein Port ist eine numerische Kennung, die einer bestimmten Anwendung auf einem Gerät zugewiesen wird, um den Austausch von Daten zwischen verschiedenen Geräten im Netzwerk zu ermöglichen.

Nach Starten des Servers wartet dieser, bis sich ein Client verbinden möchte. Nach Aktzeptieren einer Verbindung kann mit dem Client kommuniziert werden, bis die Verbindung vom Client oder vom Server beendet wird.

In unserem Beispiel werden wir im ersten Schritt einen Server erstellen, der Nachrichten vom Client empfängt und diese auf der eigenen (Server-) Konsole ausgibt.

## Bibliotheken

Zum Erstellen des Servers verwenden wir die Standardbibliotheken von C\#. Um diese zu verwenden müssen sie im ersten Schritt eingebunden werden.

!!! abstract "Arbeitsauftrag"

	Binden Sie folgende Bibliotheken am Anfang des C#-Codedokuments ein:
	```
	System.Net.Sockets
	System.Net
	System.Text
	```
	
	??? success "Lösung"
		``` cs
		using System.Net.Sockets;
		using System.Net;
		using System.Text;
		```

Mit hilfe dieser Bibliotheken können wir im nächsten Schritt einen Server erstellen.

## Server Objekt erstellen

Wir verwenden nun die eingebunden Bibliotheken, um ein Server-Objekt zu erstellen.

In C\# ist ein Server ein TcpListerner. Dieser Name kommt daher, dass der Server auf einen bestimmten TCP-Port hört.

!!! abstract "Arbeitsauftrag TCP"
	Informieren Sie sich circa 10 Minuten über TCP! Suchen sie sich selbstständig geeignetes Informationsmaterial im Internet.

	Beantworten Sie folgende Fragen:
	
	1. Wieso verwenden Applikationen TCP-Ports?
	2. Welche Ports sollten Sie für Ihre eigenen Anwendungen verwenden?
	
	??? success "Lösung -- Falls Sie nichts finden..."
		[Elektronik Kompendium TCP](https://www.elektronik-kompendium.de/sites/net/1812041.htm)
		
!!! abstract "Arbeitsauftrag Server erstellen"
	Erstellen Sie nun den Server. Sie finden hier verschiedene Schwierigkeitsstufen, die Ihnen unterschiedlich starke Hilfestellung geben.
	
	Erstellen Sie ein TcpListener-Objekt, welches auf alle IP-Adressen hört (alle Netzwerkadapter). Konfigurieren Sie den Netzwerkport 9999.
	
	Starten Sie den Server.
	
	??? abstract "Schwierig"
		[Microsoft TcpListener erstellen C\# Dokumentation](https://learn.microsoft.com/de-de/dotnet/fundamentals/networking/sockets/tcp-classes#create-a-server-socket)
			
		!!! tip 
			Verwenden Sie nicht die Socket-Objekte direkt, sondern TcpListener.
		
	??? abstract "Mittel"
		Erstellen Sie in der Main-Methode ein TcpListener-Objekt. Der Konstruktor des Objekts nimmt als erstes Argument eine IP-Adresse und einen Port.
		
		Als IP-Adresse können Sie ==IPAdress.Any== verwenden. In diesem Fall regestriert das Programm in allen IP-Adressen ihres Systems (z. B. WLAN- und LAN-Adapter) einen TCP-Port.
		
		Als zweites Argument übergeben Sie einen TCP-Port (9999).
		
		Nun können Sie über die Start()-Methode den Server starten.
	
	??? success "Lösung"
		``` cs
		static void Main(string[] args)
		{
			// we set our IP address as server's address, and we also set the port: 9999
			TcpListener server = new TcpListener(IPAddress.Any, 9999);
			
			// this will start the server
			server.Start();  
		}
		```
		
Wenn Sie dieses Programm starten werden Sie merken, dass sich nicht viel tut. Warum? Diskutieren Sie mit Ihrem Partner.

??? success "Lösung"

	Das Programm startet zwar den Server, jedoch wird es im nächsten Augenblick sofort wieder beendet.
	
## Verbindung herstellen

Wir haben nun den Server erstellt und gestartet. Dieser hat nun einen Port reserviert. Im nächsten Schritt müssen wir dem Server sagen, dass er auf eine Verbindung warten soll.

!!! abstract "Arbeitsauftrag"
	Der Server soll nun auf eine eingehende Verbindung eines Clients warten.
	
	Wird eine Verbindung mit einem Client eingegangen, soll diese in einem Client-Objekt gespeichert werden.
	
	??? abstract "Schwierig"
		[Microsoft Annehmen einer Serververbindung C\# Dokumentation](https://learn.microsoft.com/de-de/dotnet/fundamentals/networking/sockets/tcp-classes#accept-a-server-connection)
			
		!!! danger
		
			Verwenden Sie nicht die Async-Methoden!.
			
	??? abstract "Mittel"
		TcpListener bietet die Methode ==AcceptTcpClient()== an, mit deren Hilfe auf eine Verbindung eines Clients gewartet wird.
		
		Um mit der aktzeptierten Verbindung arbeiten zu können, muss der Rückgabewert gespeichert werden. Die ==AcceptTcpClient()== Methode gibt ein TcpClient-Objekt zurück.
		
	??? success "Lösung"
		``` cs
		
		// ... libraries
		
		static void Main(string[] args)
		{
			// ... create server Object 
		
			// we set our IP address as server's address, and we also set the port: 9999
			TcpListener server = new TcpListener(IPAddress.Any, 9999);
			
			// this will start the server
			server.Start();  
			
			// if a connection exists, the server will accept it
			TcpClient client = server.AcceptTcpClient();
		}
		```
	
Wenn Sie das Programm nun starten sollte sich die Konsole öffnen und offen bleiben.

## Nachrichten lesen

Wir haben nun ein Server-Objekt erstellt und gestartet. Unser server wartet nun an der Stelle
``` cs
	server.AcceptTcpClient();
```
auf die eingehende Verbindung eines Clients. Verbindet sich nun ein Client wird die Verbindung akzeptiert, danach wird sich jedoch der Server wieder direkt abschalten (die Main-Methode wird ja verlassen).

Um nun Nachrichten zu Empfangen benötigen wir einen sogenannten Stream, den wir verwenden, um Nachrichten zu empfangen und zu senden.

!!! abstract "Arbeitsauftrag"
	Erstellen Sie einen NetworkStream aus dem TcpClient-Objekt (Rückgabewert von server.AcceptTcpClient).
	
	Sie können anschließend mit Hilfe der Read-Methode des Streams die Nachrichten lesen. Diese Methode benötigt ein byte-Array, in das es die empfangene Nachricht schreibt.
	
	??? abstract "Schwierig"
		[Erstellen eines NetworkStream zum Senden und Empfangen von Daten C\# Dokumentation](https://learn.microsoft.com/de-de/dotnet/fundamentals/networking/sockets/tcp-classes#create-a-networkstream-to-send-and-receive-data)
		
	??? abstract "Mittel"
		Die ==GetStream()==-Methode des TcpClients gibt ein ==NetworkStream==-Objekt zurück. Speichern Sie dieses Objekt als Variable:
		``` cs
			NetworkStream ns = client.GetStream();
		```
		Anschließend können Sie über die ns.Read-Methode eine Nachricht des Clients lesen. Diese Methode ersartet als ersten Parameter ein byte-Array, in das die Nachricht gespeichert wird. Der zweite Paramter ist der Offset zum ersten Zeichen, der dritte und letzte Parameter kennzeichnet das Ende (Länge) des byte-Arrays.
		``` cs
			byte[] msg = new byte[1024];
			ns.Read(msg, 0, msg.Length);
		```
	
	??? success "Lösung"
		``` cs
			using System.Net.Sockets;
			using System.Net;
			using System.Text;

			internal class Program
			{
				static void Main(string[] args)
				{
					// we set our IP address as server's address, and we also set the port: 9999
					TcpListener server = new TcpListener(IPAddress.Any, 9999);
					
					server.Start();  // this will start the server

					TcpClient client = server.AcceptTcpClient();  //if a connection exists, the server will accept it

					NetworkStream ns = client.GetStream(); //networkstream is used to send/receive messages

					byte[] msg = new byte[1024];     //the messages arrive as byte array

					ns.Read(msg, 0, msg.Length);   //the same networkstream reads the message sent by the client
				}
			}
		```
		
## Nachrichten verarbeiten
Wir haben nun einen funktionierenden Server, der auf eine Verbindung wartet, diese akzeptiert und Nachrichten lesen kann.

Diese Nachrichten müssen noch irgendwie verarbeitet werden. Wir könnten eine Nachricht zum Beispiel zu Testzwecken einfach auf die Konsole ausgeben.

!!! abstract "Arbeitsauftrag"
	Lassen Sie die erhaltene Nachricht der ==Read==-Methode des ==NetworkStream==s auf die Konsole ausgeben.

	??? abstract "Schwierig"
		Recherchieren Sie, wie Sie ein byte-Array auf die Konsole ausgeben können.
		[Converting byte array to string and printing out to console stackoverflow](https://stackoverflow.com/questions/10940883/converting-byte-array-to-string-and-printing-out-to-console)
		
	??? abstract "Mittel"
		C# weiß nicht, was sich in dem Byte-Array befindet. Darum müssen wir definieren, wie die Bytes im Array interpretiert werden sollen.
		
		Dafür könne nSie folgende Methode verwenden:
		``` cs
			Encoding.Default.GetString(msg).Trim()
		```
		
		Die GetString-Methode gibt einen String zurück. Trim() löscht alle Leerzeichen vor und nach der eigentlichen Nachricht.
		
	??? success "Lösung"
		Console.WriteLine(Encoding.Default.GetString(msg).Trim());
		
## Zusammenfassung
Wir haben nun einen Server, der auf eine bestimmte IP (die eigene) hört. Er wartet auf eine eingehende Verbindung. Nach dem Verbindungsaufbau mit einem Client wartet der Server auf eine Nachricht. Diese wird bei Erhalt auf die Konsole ausgegeben.