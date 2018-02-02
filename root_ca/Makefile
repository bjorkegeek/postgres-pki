include ../params

all: root.key root.crt certindex certindex.attr certserial crlnumber root.crl.pem
	@echo ---------------------------------
	@echo
	@echo All root CA files generated.
	@echo 'Now issue "make export" to copy the public file(s) to the public directory.'
	@echo 'Then you may proceed to create an intermediate CA by making your own copy'
	@echo 'of the "intermediate.template" directory.'
	@echo

export:
	cp -v *.crt root.crl.pem ../public

root.key:
	openssl genrsa -out $@ 8192

root.crt:
	openssl req -x509 -new -nodes -key root.key -sha256 -days $(ROOT_CA_VALID_DAYS) -out root.crt  -subj "/CN=organization_ca_root"

certindex:
	touch $@

certindex.attr:
	touch $@

certserial:
	echo 1000 > certserial

crlnumber:
	echo 1000 > crlnumber

root.crl.pem:
	openssl ca -config ca.conf -gencrl -keyfile root.key -cert root.crt -out root.crl.pem

ifeq ($(CA),)
sign:
	@echo 'Error: CA variable must be set for target "sign".'
	@echo
	@echo '   make sign CA=foo'
	@echo
	exit 2
else
sign: $(CA).crt
	@echo ---------------------------------
	@echo
	@echo Intermediate certificate $(CA).crt has been generated.
	@echo Copy this file back to your intermediate CA directory
	@echo and you\'ll be good to start signing client certificates.
	@echo
endif

$(CA).crt: $(CA).csr root.crt root.key
	openssl ca -batch -config ca.conf -notext -in $< -out $@ -days $(INTERMEDIATE_CA_VALID_DAYS)

.PHONY: all export sign

