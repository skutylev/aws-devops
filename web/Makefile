# -*- makefile -*-
# DevOps: web deploy
#
# make install_dependences = Install system packages needed for deploy scripts.
#
# make new_infr = "Create AWS infrastructure"
#
# make build_ami = "Build base image AWS AMI"
#
# make prerender = "Build EC2 instance with prerender.io and node.js"
#
# make new_prod = "Create new AWS ASG with EC2 instances and add it in AWS ELB for Production Environment"
#
# make replace_prod = "Create new AWS ASG with EC2 instances and add it in AWS ELB, remove old AWS ASG with EC2 instances for Production Environment"
#
# make new_stage = "Create new AWS ASG with EC2 instances and add it in AWS ELB for Staging Environment"
#
# make replace_stage = "Create new AWS ASG with EC2 instances and add it in AWS ELB, remove old AWS ASG with EC2 instances for Staging Environment"
#
# make dev = "Create Development Environment with Vagrant"

apt:
	sudo apt-get install git vagrant virtualbox python-pip git python-dev python-virtualenv libffi-dev libssl-dev

venv: venv/bin/activate

venv/bin/activate: requirements.txt
	test -d venv || virtualenv venv --python=python2.7
	venv/bin/pip2 install -Ur requirements.txt
	touch venv/bin/activate

install_dependences: apt venv
	mv venv/local/lib/python2.7/site-packages/ansible/modules/core/cloud/amazon/elasticache.py venv/local/lib/python2.7/site-packages/ansible/modules/c$
	ln -s ~/DevOps/web/elasticache.py venv/local/lib/python2.7/site-packages/ansible/modules/core/cloud/amazon/elasticache.py

new_infr:
	ansible-playbook infr_init.yml -e "env=$(ENV) server_role=$(ROLE)"
	echo "Add this public key to github.com"
	cat ~/.ssh/$(ENV)-$(ROLE)-key.pub

build_ami:
	eval `ssh-agent -s`
	ssh-add ~/.ssh/$(ENV)-$(ROLE)-key
	ansible-playbook build_ami.yml -e "env=$(ENV) server_role=$(ROLE) tag=$(TAG)"

new_app: build_ami
	ansible-playbook app_init.yml -e "env=$(ENV) server_role=$(ROLE) tag=${TAG}"

replace_app: build_ami
	ansible-playbook app_replace.yml -e "env=$(ENV) server_role=$(ROLE) tag=${TAG} asg_group_name=$(ENV)-$(ROLE)-blue"

dev:
	vagrant up
