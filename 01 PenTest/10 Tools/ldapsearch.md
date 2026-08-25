# Purpose

Enumeration LDAP.

# Typical Use Cases

# Installation

~~~~sh

~~~~

# Typical Workflow

~~~~sh
# Test
ldapsearch -x -H ldap://$IP

# Recuperation de la base (-b necessaire pour interoger ldap)
ldapsearch -x -H ldap://$IP -s base namingcontexts

# Query globale
ldapsearch -x -H ldap://$IP -b "$Base" # (ex: DC=htb,DC=local)

# Après on rajoutera des filtres selon ce qu'on cherche
ldapsearch -x -H ldap://$IP -b "$Base" '($Query)' $Filter
~~~~

# Common Flags

# Usage Examples

~~~~sh
# Query des elements dont objectclass=User
ldapsearch -x -H ldap://$IP -b "$Base" '(objectClass=User)'
~~~~

# Useful Commands

~~~~sh

~~~~

# Notes

# References