# Aufgaben
Sie haben nun eine Grundlage zur Kommunikation zwischen Server und Client.

Hier gibt es einige Aufgaben, die die Funktionalität erweitern.

## Mehrere Nachrichten schicken
Der Client als auch der Server können aktuell nur eine Nachricht verarbeiten, bevor sich das Programm beendet.

??? abstract "Arbeitsauftrag"
	Ändern Sie den Programmcode des Clients und des Servers so ab, dass mehrere Nachrichten gesendet werden können.
	
	??? tip
		Verwenden Sie eine Schleife um den richtigen Codeblock!
		
	??? success "Lösung Server"
		``` cs
		using System.Net.Sockets;
		using System.Net;
		using System.Text;

		internal class Program
		{
			static void Main(string[] args)
			{
				TcpListener server = new TcpListener(IPAddress.Any, 9999);
				// we set our IP address as server's address, and we also set the port: 9999

				server.Start();  // this will start the server

				TcpClient client = server.AcceptTcpClient();  //if a connection exists, the server will accept it

				NetworkStream ns = client.GetStream(); //networkstream is used to send/receive messages

				while (client.Connected)  //while the client is connected, we look for incoming messages
				{
					byte[] msg = new byte[1024];     //the messages arrive as byte array

					ns.Read(msg, 0, msg.Length);   //the same networkstream reads the message sent by the client
					Console.WriteLine(Encoding.Default.GetString(msg).Trim()); //now , we write the message as string
				}
			}
		}
		```
		
	??? success "Lösung Client"
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
				while (true)
				{
					// get message from console
					var message = Console.ReadLine() + "<|EOM|>";
					// convert message to bytes
					var messageBytes = Encoding.UTF8.GetBytes(message);
					// send message to the server
					client.Send(messageBytes, SocketFlags.None);
				}
			}
		}
		```
		
## Nachricht zurück an den Absender
Sie können sich auf Clientseite bisher nicht sicher sein, ob der Server die Nachricht erhalten hat.

??? abstract "Arbeitsauftrag"
	Programmieren Sie den Server so, dass er die vom Client erhaltenen Nachrichten zurück schickt.
	
	??? tip
		Sie können die Receive()-Methode des Clients verwenden.
		Auf Serverseite können Sie die Write()-Methode des NetworkStreams verwenden.
		
??? success "Lösung Server"
		``` cs
		using System.Net.Sockets;
		using System.Net;
		using System.Text;

		internal class Program
		{
			static void Main(string[] args)
			{
				TcpListener server = new TcpListener(IPAddress.Any, 9999);
				// we set our IP address as server's address, and we also set the port: 9999

				server.Start();  // this will start the server

				TcpClient client = server.AcceptTcpClient();  //if a connection exists, the server will accept it

				NetworkStream ns = client.GetStream(); //networkstream is used to send/receive messages

				while (client.Connected)  //while the client is connected, we look for incoming messages
				{
					byte[] msg = new byte[1024];     //the messages arrive as byte array
					try
					{
						ns.Read(msg, 0, msg.Length);   //the same networkstream reads the message sent by the client
						Console.WriteLine(Encoding.Default.GetString(msg).Trim()); //now , we write the message as string
						ns.Write(msg, 0, msg.Length);
					}
					catch (Exception ex)
					{
						Console.WriteLine(ex.ToString());
					}
				}
			}
		}
		```
		
	??? success "Lösung Client"
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
				while (true)
				{
					// get message from console
					var message = Console.ReadLine() + "<|EOM|>";
					// convert message to bytes
					var messageBytes = Encoding.UTF8.GetBytes(message);
					// send message to the server
					client.Send(messageBytes, SocketFlags.None);

					byte[] receivedMessage = new byte[1024];
					client.Receive(receivedMessage);
					var receivedMessageStr = Encoding.Default.GetString(receivedMessage).Trim();
					Console.WriteLine($"Received from Server: \"{receivedMessageStr}\"");
				}

			}
		}
		```
		
Bearbeiten Sie die Nachricht auf Serverseite, bevor Sie diese zurück an den Client senden. Überlegen Sie sich, wie Sie die Nachricht verändern können.
??? tip
	Sie könnten die Nachricht in ALL-CAPS zurück senden.
	Hierfür können Sie die ToUpper()-Methode der string-Klasse verwenden.

## Erneute Serververbindung
Überprüfen Sie, was passiert, wenn Sie den Client beenden, nachdem er eine Verbindung mit dem Server eingegangen ist.

??? success "Lösung"
	Das Serverprogramm stürzt ab.
	
??? abstract "Arbeitsauftrag"
	Der Server soll bereit sein, eine neue Verbindung einzugehen, wenn er die Verbindung zu einem Client verliert.
	
	??? tip
		Erstellen Sie eine Schleife um den entsprechenden Programmcode.
		
	??? success "Lösung Server"
		Erklären Sie die Änderungen des Programms.
		``` cs
			using System.Net.Sockets;
			using System.Net;
			using System.Text;

			internal class Program
			{
				static void Main(string[] args)
				{
					TcpListener server = new TcpListener(IPAddress.Any, 9999);
					// we set our IP address as server's address, and we also set the port: 9999

					server.Start();  // this will start the server

					while (true)   //we wait for a connection
					{
						TcpClient client = server.AcceptTcpClient();  //if a connection exists, the server will accept it

						NetworkStream ns = client.GetStream(); //networkstream is used to send/receive messages

						while (client.Connected)  //while the client is connected, we look for incoming messages
						{
							byte[] msg = new byte[1024];     //the messages arrive as byte array
							try
							{
								ns.Read(msg, 0, msg.Length);   //the same networkstream reads the message sent by the client
								Console.WriteLine(Encoding.Default.GetString(msg).Trim()); //now , we write the message as string
								ns.Write(msg, 0, msg.Length);
							}
							catch (Exception ex)
							{
								Console.WriteLine(ex.ToString());
							}
						}
					}
				}
			}
		```
		
