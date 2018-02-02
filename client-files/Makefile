USER=

ifeq ($(USER),)
all:
	@echo 'Error: USER variable must be set to postgresql user name'
	@echo
	@echo '   make USER=staffan'
	@echo
	@false
else
all: $(USER)/$(USER).csr
	@echo 'Client certificate request file $< has been generated.'
	@echo 'Now, copy this file into your intermediate CA directory and issue'
	@echo
	@echo '  make sign USER=$(USER)'
	@echo
endif

$(USER)/postgresql.key:
	@mkdir -p $(USER)
	openssl genrsa -out $@ 4096

$(USER)/$(USER).csr: $(USER)/postgresql.key
	@mkdir -p $(USER)
	openssl req -new -sha256 -key $< -out $@ -subj "/CN=$(USER)"

