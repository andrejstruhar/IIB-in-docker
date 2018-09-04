# IIB-in-container
Putting IIB / ACE into container

Documenting the progress of testing IBM Integration Bus (IIB) and AppConnect Enterprise (ACE) in different container technologies.
Starting with Docker.
Extending to OpenShift.

## IIB/ACE in Docker
### The easy way  
1. `docker pull ibmcom/iib`  (or ibmcom/ace)
2. `docker run --name myNode -e LICENSE=accept -e NODENAME=MYNODE -e SEVERNAME=myserver -P docker.io/ibmcom/iib:latest`  
3. `docker port container_id`  (find the port mapped)  
4. http://iibserver_address:port (connect to IIB/ACE web admin interface to test whether it works)  

### Image from IBM SW download site
1. Download ACE_V11.0_Container.tar.gz  
2. untar  
3. `docker load`  
4. `docker run`  

### To personalize the image
Follow the IIB / ACE documentation
1. Clone the github repository  
	`git clone https://github.com/ot4i/iib-docker`	(or https://github.com/ot4i/ace-docker)
2. Change the license type to advanced in iib_manage.sh  
	`mqsimode IIB_NODE -o advanced`
3. Build the image  
	`docker build -t iibv10image .`
4. Run the container  
	`docker run --name myNode -e LICENSE=accept -e NODENAME=MYNODE -e SERVERNAME:myserver-P iibv10image`
5. Test the container by logging into it  
	`docker exec -it <container name> /bin/bash` 

## Install OpenShift Origin
1. Create VMware image with RHEL 7.5 (alternatively Centos 7)
2. Check the requirement https://docs.okd.io/latest/install/prerequisites.html (just basic, not necessary to do all)
3. Setup and install packages as documented in https://docs.okd.io/latest/install/host_preparation.html (just basic, not necessary to do all)
4. Clone the image based on the planned architecture (1 master + x nodes)
5. Clone https://github.com/gshipley/installcentos (`git clone https://github.com/gshipley/installcentos`)
6. Modify `install-openshift.sh` - mainly IP addresses for master and nodes
7. DNS is mandatory requirement but can be bypassed by using <IP_master_node>.nip.io
8. Set environment variables  
	`export DOMAIN=<public ip address>.nip.io`  
	`export USERNAME=<current user name>`  
	`export PASSWORD=password `  
9. Run the install script
Observe the IP addresses to be static, especially if testing on notebook in VMware - can assign different addresses.

## IIB/ACE in OpenShift
### Default image from IBM repository
1. `oc login`
2. `oc new-project project_name`
3. `oc adm policy add-scc-to-user anyuid -z default`
4. OpenShift web UI  
	Add to Project  
	Image Name -> ibmcom/iib	(or ibmcom/ace)
	Environment Variables  
		LICENSE	accept  
		NODENAME	mynode  
		SERVERNAME	myserver  
	Deploy

### Alternative, when you need to build your own modified Docker image
1. `oc login`
2. `oc get svc -n default | grep registry`
3. `oc whoami -t`
4. `docker login -u <username> -p NEIh_gRR3RFARuam9OZnIqWWeQSrCWrU4SpQABdoKJ4 172.30.220.167:5000`
5. `oc new-project project_name`
6. `oc adm policy add-scc-to-user anyuid -z default` - otherwise it will fail = OpenShift security runs container under the generated UID = can't use root / other user which is specified in starting script `iib_manage.sh` (e.g. `sudo /usr/sbin/rsyslogd`)
7. `docker tag iibv10image 172.30.220.167:5000/project/name`
8. `docker push 172.30.220.167:5000/project/name`
9. `oc get is`
10. OpenShift web UI  
	Add to Project  
	Image Stream Tag -> projectname  
	Image Stream -> iib  
	Tag -> latest  
	Environment Variables  
		LICENSE	accept  
		NODENAME	mynode  
		SERVERNAME	myserver  
	Deploy
