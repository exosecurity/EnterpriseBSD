.. _label-OpenPAM

*************************************
Plugable Authentication Modules (PAM)
*************************************

FreeBSD uses the OpenPAM verstion of the PAM protocol to provide flexibility in its authentication mechnisms. The purpose of PAM is to allow programs which perform authentication for the system (ssh, login, telnet, ftp, pop3, imap, etc.) use a purpose-build authentication library rather than re-implementing the same functionality multiple times. In addition, the PAM library can utilize multiple authenticaiton mechnisims (passwd, Kerberos, LDAP, etc.) to provide the system administrator additional flexibility in how users are authenticated on a system. This flexibility comes at the cost of complexity (*A* protocols/programs x *B* mechanisms = *AB* possible failure points!)


