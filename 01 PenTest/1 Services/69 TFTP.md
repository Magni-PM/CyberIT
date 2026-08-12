# Présentation

# Ports

- 69 UDP

# Fingerprinting

~~~~sh
nmap $IP -sV -sU -p 69 -n -Pn 
~~~~

# Enumeration

~~~~sh
# Nmap avec scripts specifiques
nmap $IP -sV -sU -p 69 -n -Pn  --script tftp-enum
~~~~

# Authentication

~~~~sh
# Protocole sans authentification
tftp $IP
~~~~

# Common Misconfigurations

~~~~sh

~~~~

# Common Attacks

# Useful Commands

~~~~sh
# Une fois la connexion établie
help
~~~~

# Tools

# References