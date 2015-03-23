.. _label-Authentication

**************
Authentication
**************

Simply put, *authentication* is the act of verifying that an entity is who it claims to be; it answers the question, "Is the claim *authentic*?" 
Traditionally, authentication occurs via the means of a shared secret: a password or passphrase. 
The "shared" part is that the system **and** the authentic entity both have access to the secret for the purpose of identifying the entity to the system (a one-way validation).

In Unix, and the BSD lineage, passwords are stored in a file on the system. For each system a user requires access to, the user must create and remember a password for that system. 
The human mind is not adept enough at creating and remembering multiple strong (15+ characters in length, upper and lower case letters, numbers, special characters, not a dictionary word, etc.) passwords. 
In an environment where a user must access multiple systems and applications, password management becomes a burden on both the users and system administrators.

.. toctree::
   :maxdepth:1

   openpam
   kerberos
