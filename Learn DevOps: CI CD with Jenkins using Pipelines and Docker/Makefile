LOG    = 2>&1 | tee -a log.$(shell date +%Y.%m.%d_%H.%M.%S).$@
DATE   = date | sed -n '/\(.*\)/ { h ; 's/./-/g' p ; x ; p ; x ; p }'
IAM   := jenkins_course

INDENT := 2>&1 | sed 's/^/    /'


SHELL := /bin/bash

.PHONY: make.vars make.targets make.clean make.default

make.default : make.vars make.targets

make.vars :
	@echo "available variables"
	@$(MAKE) -pRrq -f $(firstword $(MAKEFILE_LIST)) : 2>/dev/null \
	| awk '/^# makefile/,/^[^#]/ { if ($$1 !~ "^[#.]") { x=$$1 ; print $$x} }' \
	| sed -e 's/ := / !:= /' -e 's/ = / ! = /' \
	| column -t -s'!' \
	| sed 's/  \([:=]\)/\1/' \
	| sed 's/^/    /' \
	| sort

make.targets :
	@echo "available Make targets:"
	@$(MAKE) -pRrq -f $(firstword $(MAKEFILE_LIST)) : 2>/dev/null \
	| awk -v RS= -F: '/^# File/,/^# Finished Make data base/ {if ($$1 !~ "^[#.]") {print $$1}}' \
	| egrep -v '^make.default$$' \
	| sed 's/^/    make    /' \
	| sed 's/make    maven.release/make -n maven.release/' \
	| sort

make.diag :
	@echo "untracked files that do not exist in .gitignore"
	git ls-files . --exclude-standard --others ${INDENT}



make.clean : 
	@find * -name '*~' -exec rm -v {} \;

docker.build :
	docker build --tag ${IAM} .

docker.run :
	[ -d jenkins_home ] || mkdir -pv jenkins_home
	docker run \
	    --env HOST_UID=$(id -u) \
	    --env HOST_GID=$(id -g) \
	    --rm \
	    --publish 8080:8080 \
	    --publish 50000:50000 \
	    --volume jenkins_home:/var/jenkins_home \
	    --name jenkins \
	    jenkins/jenkins:lts



#EOF

