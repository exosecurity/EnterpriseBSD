.. _label-Authentication

**************
Authentication
**************

Simply put, *authentication* is the act of verifying that an entity is who it claims to be; it answers the question, "Is the claim *authentic*? 
Traditionally, authentication occurs via the means of a shared secret: a password or passphrase. 
The "shared" part is that the system **and** the authentic entity both have access to the secret for the purpose of identifying the entity to the system (a one-way validation).

In Unix, and the BSD lineage, passwords are stored in a file on the system. For each system a user requires access to, the user must create and remember a password for that system. 
The human mind is not adept enough at creating and remembering multiple strong (15+ characters in length, upper and lower case letters, numbers, special characters, not a dictionary word, etc.) passwords. 
In an environment where a user must access multiple systems and applications, password management becomes a burden on both the users and system administrators.

.. _label-Kerberos-Protocol

Kerberos Protocol
=================

It was to deal with this situation that the `Kerberos protocol`_ was designed.
A comprehensive description of the history and development of Kerberos is out of scope of this guide, but the following summarizes the workings and benefits of the protocol.

#. When a user logs on to a system (client) that is part of a Kerberos *realm*, the client sends the user name to the Authentication Server (AS) and the service the user is requesting (i.e., login). 
#. The AS verifies the user is in the database and generates a secret key derived from the user's password. 
#. If the client is in the database, the AS returns two encrypted messages: 1) a Client/Ticket-Granting Service (TGS) session key (encrypted with the secret key), and 2) a Ticket-Granting-Ticket (TGT, encrypted with the secret key of the TGS).
#. The client generates a secret key from the user-provided password and attempts to decrypt the first message (this is a symetric cipher - the sender and receiver both have the same key). 
#. If the password provided by the user matches the password retrieved by the AS, the client obtains the Client/TGS session key which is used for future communications with the TGS.

This authorization process ensures that:

#. the user is defined in a centralized (hopefully protected) database, 
#. the authorization request is coming from a client system known to the AS (not a rogue system attached to the network), and 
#. the user's password is never transmitted across the network.

In the case where the AS or KDC is down or inaccessable, the user could not be authenticated. This situation is mitigated by having a primary KDC and multiple secondary KDC which can be queried.

.. _label-Kerberos-Software

Kerberos Software
==================

FreeBSD includes the `Heimdal`_ implementation of the Keberos protocol in the base system.	
The original MIT implementation is available in the ports tree (security/krb5) if users wish to install that.

The `FreeBSD Handbook`_ has a good `chapter`_ on setting up a Kerberos KDC.
Since it was written, one change in the set up of a KDC to be aware of is the `/etc/rc.conf` lines, *kadmin5_server_enable* is deprecated in favor of *kadmind_enable*::

	kdc_enable="YES"
	kadmind_enable="YES"


.. _label-Managing-Kerberos-Realm

Managing a Kerberos Realm
=========================

When deploying new systems to your environment, each host will need an entry in the KDC database so it can communicate with the KDC on behalf of users. 
The host stores a shared secret key in the file `/etc/krb5.keytab` that it uses to decrypt messages from the KDC.
The root user may view the contents of the keytab file with the following command::

	# ktutil -k /etc/krb5.keytab list
	/etc/krb5.keytab:

	Vno  Type                     Principal                                       Aliases 
	  1  aes256-cts-hmac-sha1-96  host/client.example.com@EXAMPLE.COM  
	  1  des3-cbc-sha1            host/client.example.com@EXAMPLE.COM  
	  1  arcfour-hmac-md5         host/client.example.com@EXAMPLE.COM  
	# 


To verify a system is in the KDC database, the :manpage:`kadmin(1)` command can list out all the entities (principals) after authenticating a user authorized to do so::

	kadmin> list *
	admin/admin@EXAMPLE.COM's Password: 
	alice
	bob
	eve
	admin
	default
	admin/admin
	kadmin/admin
	kadmin/hprop
	kadmin/changepw
	changepw/kerberos
	WELLKNOWN/ANONYMOUS
	krbtgt/EXAMPLE.COM
	nfs/nfs.example.com
	host/client-96.example.com
	host/client-97.example.com
	host/kdc-144.example.com
	kadmin>

If a client does not exist in the database, users will not be able be authorized from that client.

 
.. _Kerberos protocol: http://en.wikipedia.org/wiki/Kerberos_%28protocol%29
.. _Heimdal: www.h5l.org
.. _FreeBSD Handbook: https://www.freebsd.org/doc/en_US.ISO8859-1/books/handbook/
.. _chapter: https://www.freebsd.org/doc/en_US.ISO8859-1/books/handbook/kerberos5.html
