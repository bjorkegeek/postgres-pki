CN=

ifeq ($(CN),)
all:
	@echo 'Error: CN variable must be set with server hostname'
	@echo
	@echo '   make CN=sql.example.com'
	@echo
	@false
else
all: server.csr
	@echo 'Server certificate request file "server.csr" has been generated.'
	@echo 'Now, copy this file into your intermediate CA directory and issue'
	@echo
	@echo '  make sign-server'
	@echo
endif

server.key:
	openssl genrsa -out server.key 4096

server.csr: server.key
	openssl req -new -sha256 -key server.key -out server.csr -subj "/CN=$(CN)"

