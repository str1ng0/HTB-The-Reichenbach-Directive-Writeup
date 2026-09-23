After we connected via RDP to the analysis machine (with the provided credentials), we first noticed the `DESTKOP-QNTIG5I.e01` file on the Desktop. We identified it as a disk image, and loaded it into Autopsy for inspection. Since ingesting takes a very long time on the given machine, we started manually analyzing files as soon as the file structures and contents was loaded (at the cost of not being able to use the "Keyword Search" function).

For the first flag, we browsed common user directories, and we found an interesting downloaded file at `\Users\spur\Downloads`, an OpenVPN install file. Thus, we assume that the VPN the user connects to is OpenVPN. The first instinct was to find an `.ovpn` file which the user `spur` may have used to open a connection. However, no such file was found, although we identified that the file resided at `C:\VPN\spur.ovpn`, but it cannot be recovered. So, the next best thing would be a log of the OpenVPN connection, and we have found a `spur.log` file in the Orphaned Files section of Autopsy. In this, we have found the IP and port of the remote server that the user connected to (`18.156.81.166:7577`) from

`2026-09-01 05:21:10 [NPLN-VPN-7577] Peer Connection Initiated with [AF_INET]18.156.81.166:7577`

The second flag was also found in this file, in the line:

`2026-09-01 05:21:10 VERIFY OK: depth=1, CN=NPLN-CA`

Thus, we confirmed that `NPLN-CA` was the Certificate Authority (CA) that issued the VPN client certificate.

For the third flag, we also identified it from the same log file, in the line

`2026-09-01 05:21:10 PUSH: Received control message: 'PUSH_REPLY,redirect-gateway def1 bypass-dhcp,dhcp-option DNS 8.8.8.8,dhcp-option DNS 1.1.1.1,route-gateway 10.129.175.1,topology subnet,ping 10,ping-restart 120,ifconfig 10.129.175.2 255.255.255.0,peer-id 1,cipher AES-256-GCM'`

So, the IP assigned to the user by the VPN server is `10.129.175.2`.

To find the forth flag, we used the 'Recent Activity' ingestion of Autopsy. While we were searching for the previous flags, this ingestion ran on the background and finished its analysis. From there, we checked the `Installed Programs` section to find the forth flag `Tactical RMM Agent v.2.11.0`

Fifth flag required us to find the domain that Tactical RMM Agent connected to. For that, we searched for log files in the System and OrpahanFiles but were unable to find any. Then, we decided to check the registry keys for the app in `\System32\Config\SOFTWARE`. We found the fifth flag `api.antimattercommunication.xyz` and the sixth flag `98ec588da683c01820232943a6151e8e7772419b` SHA-1 Authentication Token in /Windows/System32/config/SOFTWARE/TacticalRMM.

After finding the first operation vanish command `Remove-Item -LiteralPath C:\Users\spur\Gajim -Recurse -Force` (seventh flag), for the eight flag, we started searching for files that may relate to Gajim in the `$OrphanFiles` directory since the previous command wiped all Gajim folders. We found `Settings.sqlite` file in `$OrphanFiles/UserData/` folder including the keyword 'gajim.E0NTZC2M'. This file contained the email adress: `spurio9@murknet.htb` and the ninth flag password: `spur999!*`
 
The tenth flag took a while for us to find. After searching through various log and db files in `$OrphanedFiles`, we noticed the line `olk-SmtpActionableMessagesEnabled_Abel.stokes@hotmail.com` containing a full name `Abel Stokes` giving us the tenth flag.
