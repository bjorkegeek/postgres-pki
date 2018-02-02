include ../params

CLIENT_DIR=../client-files
SERVER_DIR=../server-files
USER=
NAME=$(shell pwd | xargs basename)

ifeq ($(shell pwd|xargs basename),intermediate.template)
$(error Do not run in template directory! Do: cp -r intermediate.template my_intermediate in the repo root)
endif

#ifeq ($(NAME),givemeaname)
#$(error Provide a name for this intermedidate by changing the NAME= line in the params file.)
#endif

all: $(NAME).csr certindex certindex.attr certserial crlnumber

certindex:
	touch $@

certindex.attr:
	touch $@

certserial:
	echo 1000 > certserial	

crlnumber:
	echo 1000 > crlnumber

$(NAME).key:
	openssl genrsa -out $@ 4096

$(NAME).csr: $(NAME).key
	openssl req -new -sha256 -key $< -out $@ -subj "/CN=$(NAME)_intermediate_ca"
	@echo ---------------------------------
	@echo
	@echo Generated $@. Now, copy this into the root_ca directory and sign it:
	@echo
	@echo "  cp $(NAME).csr ../root_ca/"
	@echo '  cd ../root_ca/'
	@echo "  make sign CA=$(NAME)"
	@echo


ifeq ($(USER),)
sign:
	@echo 'Error: USER variable must be set for target "sign".'
	@echo
	@echo '   make sign USER=foo'
	@echo
	@false
else
sign: all $(CLIENT_DIR)/$(USER)/postgresql.crt $(CLIENT_DIR)/$(USER)/root.crt
	@echo ---------------------------------
	@echo
	@echo User certificate $(CLIENT_DIR)/$(USER)/postgresql.crt been generated.
	@echo
endif

$(CLIENT_DIR)/$(USER)/root.crt: ../public/root.crt
	cp $< $@

$(CLIENT_DIR)/$(USER)/postgresql.crt: $(USER).crt $(NAME).crt
	@mkdir -p $(CLIENT_DIR)/$(USER)
	cat $(USER).crt $(NAME).crt > $@

$(USER).crt: $(USER).csr $(NAME).crt		
	openssl ca -batch -config ca.conf -cert $(NAME).crt -keyfile $(NAME).key -notext -in $< -out $@

$(USER).csr:
	@echo 'Error: Missing certificate request file "$(USER).csr".'
	@echo 'Generate a key file and a csr-file by issuing:'
	@echo
	@echo '  openssl genrsa -out potsgresql.key 4096'
	@echo '  openssl req -new -sha256 -key postgresql.key -out $(USER).csr -subj "/CN=$(USER)"'
	@echo
	@false	

$(NAME).crt:
	cp -v ../root_ca/$(NAME).crt $@

sign-server: all $(SERVER_DIR)/server.crt $(SERVER_DIR)/root.crt

$(SERVER_DIR)/root.crt: ../public/root.crt
	cp $< $@

$(SERVER_DIR)/server.crt: server.crt $(NAME).crt
	@mkdir -p $(SERVER_DIR)
	cat server.crt $(NAME).crt > $@

server.crt: server.csr $(NAME).crt		
	openssl ca -batch -config ca.conf -cert $(NAME).crt -keyfile $(NAME).key -notext -in $< -out $@

server.csr:
	@echo 'Error: Missing certificate request file "server.csr".'
	@echo 'Generate a key file and a csr-file by issuing:'
	@echo
	@echo '  openssl genrsa -out server.key 4096'
	@echo '  openssl req -new -sha256 -key server.key -out server.csr -subj "/CN=my.server.hostname.com"'
	@echo
	@false	


usage:
	@echo Usage: $(MAKE) NAME=nameOfIntermediateCA
	@echo
	@false

.PHONY: all usage
