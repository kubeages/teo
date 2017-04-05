# Demo TEO

TEO is a customized redmine. This is a TEO deployment example for Openshift.

<blockquote> <i>Note: step 1 prepares an Openshift demo environment, if you already have an operational Openshift environment, you can start from step 2.</i> </blockquote>

1.  Prepare Openshift environment. <br />
    For demo purposes you can use Openshift CDK (https://developers.redhat.com/products/cdk/download/)

<pre>    $ wget https://developers.redhat.com/download-manager/file/cdk-3.0.beta-minishift-linux-amd64
    # mv cdk-3.0.beta-minishift-linux-amd64 /bin/minishift
    
    $ minishift setup-cdk
    [...]
    CDK 3 setup complete.
    
    $ minishift start --username <i>Red_Hat_username</i>  --password <i>Red_Hat_password</i> 
    Starting local OpenShift cluster using 'kvm' hypervisor...
    [...]
    To login as administrator:
    oc login -u system:admin </pre>

2.  Create a project called "teo"\
<b>$ oc login -u admin</b><br />
Password: <i>admin</i><br />
<b>$ oc new-project teo</b>

3.  Build teo image from GitHub:<br />
<b> $ oc new-build https://github.com/jmnohales/teo.git</b><br />
    <i>(optional) It is possible to monitor the process from GUI\
    <b> $ minishift console</b></i><br /> 

4.  Import TEO app tempate:<br />
<b> $ oc create -f https://raw.githubusercontent.com/jmnohales/teo/master/teo_template.yml</b><br />


5.  Change teo project privileges to be able to run containers as root:<br />
<b>$ oc login -u system:admin</b><br />
<b>$ oc project teo</b><br />
<b>$ oc adm policy add-scc-to-user anyuid -z default</b><br />

<i>[.... Wait until image "teo" be ready at the registry ...]</i>

<blockquote>
****     ANSIBLE ZONE   *****  <br />
<br />
6. Prepare posgreSQL database with ansible. We tested it with a Minimal installation of CentOS 7.<br />
     Prior to launch the playbook:<br />
     &emsp;  - Ensure /etc/ansible/hosts has the correct IP associated to "bbdd"<br/>
     &emsp;  - Allow access to postgreSQL host from Ansible:<br/>
     &emsp; <b> $ ssh-copy-id root@'<i>IP_postgreSQL_server</i>' </b> <br/>
   Then launch Ansible Playbook to install and configure PostgreSQL:<br />
<b>$ wget https://raw.githubusercontent.com/jmnohales/teo/master/postgresql_playbook.yml</b><br/>
<b>$ ansible-playbook postgresql_playbook.yml</b><br/>
<br />
To test postgresql database:<br />
<b>$ psql -h <i>postgre_server_IP</i> -U redmine --list</b><br/>
<br />
**** END OF ANSIBLE ZONE ****<br/>
</blockquote>


7. Launch a teo template instance from the GUI:
      - Open Openshift GUI
      - Select Project "teo"
      - Select "Add to project in the middle of the menu bar"
      - Look for "template-teo"
      - Fill the data for DB connection. For example:\
          DB_HOST = <posgreSQL_server_IP> \
          DB_NAME = redmine_production \
          DB_USER = redmine \
          DB_PASS = password
          
          
