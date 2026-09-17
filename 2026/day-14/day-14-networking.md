**Quick Concepts (write 1–2 bullets each)**

OSI layers (L1–L7) vs TCP/IP stack (Link, Internet, Transport, Application)
  **OSI LAYER**
  Application - application layer prepares data, e.g whatsapp, web browser
  Presentation - In this layer data is encrypted. Protocols used are TLS,SSL
  Session -manages session between application like setup and maintenance  e.g login session. Protocol used NetBios
  Transport - This layer manages traport of data, error checking and data recovery. TCP and UDP protocols are used.
  Network - In this layer logic addressing and routing is done. Protocols used are IPV4,IPV6,ICMP,OSPF.  
  Data link layer- This layer ensure node to node delivery and data framing for that it use mac addresses. It operates on Bridge and switch
  Phyical layer -  Data is transferred using electical/optical form, using cables or ethernet(100BaseTX), usb.
  **TCP/IP stack**
  Application - This layer is consists of 3 layers, Application,presentation and session.
  Transport - Manages reliable data traportation, error checking and recovery.
  Internet - This layer does the work of Network layer.
  Network access (Link layer)- It contains functionality of DLL and Physical Layer.
  
Where IP, TCP/UDP, HTTP/HTTPS, DNS sit in the stack
  IP - Internet Layer
  TCP/UDP - Transport Layer
  HTTP/HTTPS - Application Layer
  DNS - Application Layer

One real example: “curl https://example.com = App layer over TCP over IP”
  App layer - when you do curl and url, then data is prepared in this layer transferred to next layer using https protocol
  
-------------------------------------------------------------------------------------------------------------------------------------------------------
**Hands-on Checklist (run these; add 1–2 line observations)**
  Identity: hostname -I (or ip addr show) — note your IP.
  <img width="680" height="92" alt="image" src="https://github.com/user-attachments/assets/a3b1db5d-991c-4ed5-a919-0de5733986f7" />

  Reachability: ping <target> — mention latency and packet loss.
  <img width="876" height="502" alt="image" src="https://github.com/user-attachments/assets/b6c5e369-6471-47bb-97dc-fbe20e01b559" />

  Path: traceroute <target> (or tracepath) — note any long hops/timeouts.
  
  
  Ports: ss -tulpn (or netstat -tulpn) — list one listening service and its port.
  <img width="1530" height="375" alt="image" src="https://github.com/user-attachments/assets/017ada72-51e2-4b90-b07d-d70413411f41" />

  Name resolution: dig <domain> or nslookup <domain> — record the resolved IP.
  <img width="840" height="617" alt="image" src="https://github.com/user-attachments/assets/cc8f6c85-367c-41e9-81ea-4412c30715b2" />

  HTTP check: curl -I <http/https-url> — note the HTTP status code.
  <img width="1017" height="387" alt="image" src="https://github.com/user-attachments/assets/7d4b0852-a317-449b-838f-d590cccd1914" />

  Connections snapshot: netstat -an | head — count ESTABLISHED vs LISTEN (rough).
  <img width="1112" height="347" alt="image" src="https://github.com/user-attachments/assets/b564241e-d136-41f7-8286-0d701b4df08b" />

  Pick one target service/host (e.g., google.com, your lab server, or a local service) and stick to it for ping/traceroute/curl where possible.

------------------------------------------------------------------------------------------------------------------------------------------------------
**Mini Task: Port Probe & Interpret**
  Identify one listening port from ss -tulpn (e.g., SSH on 22 or a local web app).
    vaibhav@ubuntu-vm:~$ ss -tulpn
      Netid      State       Recv-Q       Send-Q             Local Address:Port             Peer Address:Port      Process      
      udp        UNCONN      0            0                     127.0.0.54:53                    0.0.0.0:*                      
      udp        UNCONN      0            0                  127.0.0.53%lo:53                    0.0.0.0:*                      
      udp        UNCONN      0            0                      127.0.0.1:323                   0.0.0.0:*                      
      udp        UNCONN      0            0                        0.0.0.0:5353                  0.0.0.0:*                      
      udp        UNCONN      0            0                          [::1]:323                      [::]:*                      
      udp        UNCONN      0            0                           [::]:5353                     [::]:*                      
      tcp        LISTEN      0            4096               127.0.0.53%lo:53                    0.0.0.0:*                      
      **tcp        LISTEN      0            4096                   127.0.0.1:631                   0.0.0.0:**                    
      tcp        LISTEN      0            4096                  127.0.0.54:53                    0.0.0.0:*                      
      tcp        LISTEN      0            4096                       [::1]:631                      [::]:*

      
  From the same machine, test it: nc -zv localhost <port> (or curl -I http://localhost:<port>).
    vaibhav@ubuntu-vm:~$ nc -zv localhost 631
    Connection to localhost (127.0.0.1) 631 port [tcp/ipp] succeeded!

  Write one line: is it reachable? If not, what’s the next check? (e.g., service status, firewall).
    Port 631 (IPP) was identified as a listening TCP port using ss -tulpn. Testing with nc -zv localhost 631 succeeded, confirming that the port is reachable         from the same machine.

--------------------------------------------------------------------------------------------------------------------------------------------------------------

**Reflection**

- Which command gives you the fastest signal when something is broken? 
  `ping` gives a quick signal for basic network connectivity. For a specific service, `nc -zv <host> <port>` is faster and more useful because it checks whether the required port is reachable.

- What layer (OSI/TCP-IP) would you inspect next if DNS fails? If HTTP 500 shows up? 
  - If DNS fails: I would inspect the Application layer because DNS is an application-layer service.
    I would check the DNS resolver, DNS configuration, and name resolution.
  - If HTTP 500 appears: I would inspect the Application layer (Layer 7) because the server is reachable, but the application is returning an internal server         error. I would check application logs and the backend/service dependencies.

- Two follow-up checks you’d run in a real incident:
  1. Check whether the required service is running and listening: `systemctl status <service>` and `ss -tulpn`
  2. Check application/system logs for errors: `journalctl -u <service>` or the application's log files.
