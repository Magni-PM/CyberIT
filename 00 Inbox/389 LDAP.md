# Présentation

# Ports

- 389
- 636
- 3268
- 3269

# Fingerprinting

~~~~sh
nmap -sV $IP -p 389,636,3268,3269
~~~~

# Enumeration

~~~~sh
# Nmap basique
nmap -sCV $IP -p 389,636,3268,3269

# Basique avec ldapsearch
ldapsearch -x -H ldap://$IP # Voir note ldapsearch
~~~~

# Authentication

~~~~sh

~~~~

# Common Misconfigurations

~~~~sh

~~~~

# Common Attacks

# Useful Commands

~~~~sh

~~~~

# Tools

[[ldapsearch]]

# References