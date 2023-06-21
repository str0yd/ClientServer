# Client

In diesem Abschnitt erstellen wir einen Server, zu dem sich ein Client verbinden kann.

## Übersicht

Zum erstellen eines Clients können Sie die Standardbibliotheken von C\# verwenden.

Danach können wir ein Client-Objekt erstellen, der sich auf einen bestimmten Port eines Servers verbindet. 

!!! note
	
	Ein Port wird in der Computernetzwerktechnik verwendet, um den Datenverkehr zwischen verschiedenen Anwendungen oder Diensten zu ermöglichen. Ein Port ist eine numerische Kennung, die einer bestimmten Anwendung auf einem Gerät zugewiesen wird, um den Austausch von Daten zwischen verschiedenen Geräten im Netzwerk zu ermöglichen.

Ein Client geht in der Regel eine aktive Verbindung mit einem Server ein. Aus diesem Grund muss der Server zuerst gestartet werden, damit sich der Client verbinden kann.

Der Client versucht also eine Verbindung mit einem Server mittels TCP-Socket einzugehen. Wird der Verbindungsversuch aktzeptiert, können die beiden miteinander kommunizieren.

Wir werden nun einen Client erstellen, der sich mit einem Server verbindet und ihm Nachrichten schicken kann.

## Bibliotheken

Zum Erstellen des Clients verwenden wir die Standardbibliotheken von C\#. Um diese zu verwenden müssen sie im ersten Schritt eingebunden werden.

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

Mit hilfe dieser Bibliotheken können wir im nächsten Schritt einen Client erstellen.

## Client Objekt erstellen
Wir verwenden nun die eingebunden Bibliotheken, um ein Client-Objekt zu erstellen.

In C\# Verwenden wir sogenannte Sockets, um eine TCP-Verbindung einzuleiten und aufrecht zu erhalten.

In unserem Fall verwendet der Server einen TcpListener. Dieser ist eine Erweiterung der Socket-Klasse.

Für den Client verwenden wir nun einen TCP-Socket, um mit dem Server zu kommunizieren.

!!! abstract "Arbeitsauftrag TCP"
	Informieren Sie sich (falls noch nicht geschehen) circa 10 Minuten über TCP! Suchen sie sich selbstständig geeignetes Informationsmaterial im Internet.

	Beantworten Sie folgende Fragen:
	
	1. Wieso verwenden Applikationen TCP-Ports?
	2. Welche Ports sollten Sie für Ihre eigenen Anwendungen verwenden?
	
	??? success "Lösung -- Falls Sie nichts finden..."
		[Elektronik Kompendium TCP](https://www.elektronik-kompendium.de/sites/net/1812041.htm)
		
!!! abstract "Arbeitsauftrag Client erstellen"
	Erstellen Sie einen Client, der sich auf eine bestimmte IP-Adresse und einen bestimmten Port eines Servers verbindet.
	
	??? abstract "Schwierig"
		[Erstellen eines IP-Endpunkts C\# Dokumentation](https://learn.microsoft.com/de-de/dotnet/fundamentals/networking/sockets/socket-services#create-an-ip-endpoint)
		
	??? abstract "Mittel"
		Erstellen Sie in der Main-Methode ein Socket-Objekt.
		
		Der Konstruktor des Socket-Objets hat drei Argumente:
		
		1. AddressFamily: Gibt das Adressierschema an, das durch eine Instanz der Socket-Klasse verwendet werden kann.
		2. SocketType: Gibt den Sockettyp an, der von einer Instanz der Socket-Klasse dargestellt wird.
		3. ProtocolType: Gibt die Protokolle an, die die Socket-Klasse unterstützt.
		
		Als SocketType verwenden wir ==SocketType.Stream==.
		ProtocolType ist Tcp: ==ProtocolType.Tcp==
		
		Die AddressFamily müssen wir etwas umständlicher erstellen:
		
		Im Ersten Schritt benötigen wir ein IPAddress-Objekt. Dieses Objekt stellt die IP-Adresse des zu verbindenden Servers dar. Sie erstellen dieses Objekt wie folgt:
		``` cs
		IPAddress ipAddress = IPAddress.Parse("127.0.0.1");
		```
		
		Tragen Sie hier statt der Loopback-Adresse 127.0.0.1 die Adresse des Servers ein (falls Sie es lokal ausprobieren bleibt natürlich diese Loopback-Adresse stehen.ü
		
		Im nächsten Schritt müssen wir ein IPEndPoint-Objekt erstellen. Dies verwendet das IPAddress-Objekt und fügt einen Port hinzu:
		``` cs
		IPEndPoint ipEndPoint = new(ipAddress, 9999);
		```
		
		In diesem Beispiel erstellen wir ein IPEndPoint-Objekt, das auf den Port 9999 bindet. Sie können den Port frei wählen, es muss jedoch der gleiche Port sein, den Sie beim Server auch verwenden.
		
		Nun können Sie die AddressFamily im Konstruktor des Socket-Objekts wie folgt verwenden:
		```cs
		ipEndPoint.AddressFamily
		```
	
	??? success "Lösung"
		``` cs
		using System.Net;
		using System.Net.Sockets;
		using System.Text;

		internal class Program
		{
			static void Main(string[] args)
			{
				IPAddress ipAddress = IPAddress.Parse("127.0.0.1");
				IPEndPoint ipEndPoint = new(ipAddress, 9999);

				using Socket client = new(
					ipEndPoint.AddressFamily,
					SocketType.Stream,
					ProtocolType.Tcp);
			}
		}
		```
	
## Verbindung herstellen

Wir haben nun den Client erstellt. Dieser weiß nun, an welche IP-Adresse und welchen Port er sich verbinden muss. Im nächsten Schritt müssen wir uns mit einem Server verbinden.

!!! abstract "Arbeitsauftrag"
	Der Client soll sich nun mit einem Server verbinden. Überlegen Sie, welche Methode sie auf das Client-Objekt hierfür anwenden müssen.
	
	??? success "Lösung"
		``` cs
		client.Connect(ipEndPoint);
		```
		
Probieren Sie das Verhalten Ihres Clients mit und ohne laufenden Server aus.

## Nachrichten schicken

Wir haben nun ein Client-Objekt erstellt und gestartet. Dieser verbindet sich nun mit einem Server, tut ansonsten jedoch noch nicht viel.

Ziel ist es, mit dem Server zu kommunizieren. Dafür senden wir Nachrichten an den Server, der diese in der eigenen Konsole ausgibt.

!!! abstract "Arbeitsauftrag"
	Der Client soll Nachrichten, die in der eigenen Konsole eingegeben werden an den Server schicken.
	
	??? abstract "Schwierig"
		Die ==Send()==-Methode des Clients verwendet ein byte-Array, um Nachrichten zu versenden.
		
		Erstellen Sie dieses und rufen Sie die Send()-Methode auf, um Nachrichten, die Sie in die Konsole eingeben, zu schicken.
		
	??? abstract "Mittel"
		Die ==Send()==-Methode des Clients verwendet ein byte-Array, um Nachrichten zu versenden.
		
		Wir müssen also im ersten Schritt eine Nachricht von der Konsole erhalten:
		``` cs
		var message = Console.ReadLine() + "<|EOM|>";
		```
		
		Danach müssen wir diesen String in ein byte-Array konvertieren:
		``` cs
		var messageBytes = Encoding.UTF8.GetBytes(message);
		```
		
		Und zum Schluss können wir die Nachricht versenden:
		``` cs
		client.Send(messageBytes, SocketFlags.None);
		```

	
	??? success "Lösung"
		``` cs
			using System.Net;
			using System.Net.Sockets;
			using System.Text;

			internal class Program
			{
				static void Main(string[] args)
				{
					IPAddress ipAddress = IPAddress.Parse("127.0.0.1");
					IPEndPoint ipEndPoint = new(ipAddress, 9999);

					// cerates the client
					Socket client = new(
						ipEndPoint.AddressFamily,
						SocketType.Stream,
						ProtocolType.Tcp);

					// connect to the server
					client.Connect(ipEndPoint);

					// get message from console
					var message = Console.ReadLine() + "<|EOM|>";
					// convert message to bytes
					var messageBytes = Encoding.UTF8.GetBytes(message);
					// send message to the server
					client.Send(messageBytes, SocketFlags.None);
					Console.WriteLine($"Socket client sent message: \"{message}\"");
			}
		```
		
An dieser Stelle können Sie nun mit dem Server kommunizieren und ihm eine Nachricht schicken. Dieser sollte sie auf der eigenen Konsole ausgeben.

Testen Sie Ihre Umsetzung!
		
## Zusammenfassung
Wir haben nun einen Client erstellt, der sich mit einem Server verbindet und ihm eine Nachricht schickt.

Wenn Sie die Verbindung des Clients mit dem Server erfolgreich getestet haben, bearbeiten Sie [weitere Aufgaben](Exercise.md).