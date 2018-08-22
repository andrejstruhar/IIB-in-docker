# IIB-in-docker
Putting IIB / ACE into container

Documenting the progress of testing IBM Integration Bus (IIB) and AppConnect Enterprise (ACE) in different container technologies.
Starting with Docker.
Extending to OpenShift.

## Install OpenShift Origin
1. Create VMware image with RHEL 7.5 (alternatively Centos 7)
2. Cech the requirement https://docs.okd.io/latest/install/prerequisites.html (just basic, not necessary to do all)
3. Setup and install packages as documented in https://docs.okd.io/latest/install/host_preparation.html (just basic, not necessary to do all)
4. Clone the image based on the planned architecture (1 master + x nodes)
5. Clone https://github.com/gshipley/installcentos (git clone https://github.com/gshipley/installcentos)
6. Modify install-openshift.sh - mainly IP addresses for master and nodes
7. DNS is mandatory requirement but can be bypassed by using <IP_master_node>.nip.io
8. Set environment variables
	$ 'export DOMAIN=<public ip address>.nip.io'
	$ 'export USERNAME=<current user name>'
	$ 'export PASSWORD=password '
9. Run the install script
Observe the IP addresses to be static, especially if testing on notebook in VMware - can assign different addresses.

## IIB/ACE in Docker
Follow the IIB / ACE documentation
1. git clone 
2. Change the license type to advanced in iib_manage.sh
    mqsimode IIB_NODE -o advanced
3. Build the image
    docker build -t iibv10image .

## IIB/ACE in OpenShift
1. oc login
2. oc get svc -n default | grep registry
3. oc whoami -t
4. docker login -u juso00 -p NEIh_gRR3RFARuam9OZnIqWWeQSrCWrU4SpQABdoKJ4 172.30.220.167:5000
5. oc project
6. docker tag iibv10image 172.30.220.167:5000/project/name
7. docker push 172.30.220.167:5000/project/name
8. oc get is
