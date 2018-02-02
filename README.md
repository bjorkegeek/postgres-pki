This repo contains Makefiles and recipe for setting up a three level
PKI for PostgreSQL.  It aims to generate certificates with high
security, but at the time of writing it has not been
reviewed. Reviewers welcome.

However, if you build a PKI using this project you should get
certificates that _work_.

All files may be generated on a single machine but for higher security
different machines can be used for root CA, intermediate CA, server
and client certificates without having to move any secrets between
machines.

Example steps setting up a PKI with an intermediate CA named *bengt*,
a postgresql user with the name *staffan* and a certificate for the
postgresql server.

1. Clone the repo
2. Create self-signed root CA and put it in the public directory:
   ```bash
   cd root_ca
   make
   make public
   cd ..
   ```
3. Create a directory for your intermediate CA
   ```bash
   cp -r intermediate.template bengt
   ```
4. Make certificate signing request (CSR) for intermediate CA
   ```bash
   cd bengt
   make
   cd ..
   ```
5. Copy CSR to root ca directory (copy between machines if needed)
   ```bash
   cp bengt/bengt.csr root_ca/
   ```
6. Sign intermediate CA by the root CA
   ```bash
   cd root_ca
   make sign CA=bengt
   cd ..
   ```
7. Copy intermediate certificate to intermediate directory (copy between machines if needed)
   ```bash
   cp root_ca/bengt.crt bengt
   ```
8. Generate CSR for server (```sql.example.com``` in this example)
   ```bash
   cd server-files
   make CN=sql.example.com
   cd ..
   ```
9. Copy server CSR to intermediate directory (copy between machines if needed)
   ```bash
   cp server-files/server.csr bengt
   ```
10. Sign server CSR, generating server cert
    ```bash
    cd bengt
    make sign-server
    cd ..
    ```
11. Generate CSR for user
    ```bash
    cd client-files
    make USER=staffan
    cd ..
    ```
12. Copy user CSR to intermediate directory (copy between machines if needed)
    ```bash
    cp client-files/staffan/staffan.csr bengt
    ```
13. Sign user CSR, generating client cert
    ```bash
    cd bengt
    make sign USER=staffan
    cd ..
    ```

Your files are done!
Files for the server (in directory ```server-files```:
 * ```root.crt``` (Root CA certificate)
 * ```server.crt``` (Server certificate bundle)
 * ```server.key``` (Server key **Keep secret!**)

Files for the client (in directory ```client-files/staffan```:
 * ```root.crt``` (Root CA certificate)
 * ```postgresql.crt``` (Client certificate bundle)
 * ```postgresql.key``` (Client key **Keep secret!**)
