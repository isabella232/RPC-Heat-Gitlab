Description
===========

This is an OpenStack HEAT template to deploy [GitLab CE](https://about.gitlab.com/) 
server in an OpenStack cloud. 

This template uses [GitLab salt-formulas](https://github.com/rcbops/gitlab-formula) 
to configure the servers. Single instance GitLab CE server will be created and configured 
to use with Postfix service for sending out mail.

For access to GitLab node, a floating ip will be assigned to the 
salt-master. Or a new server can be created in the same network as the GitLab node.
Additionally, GitLab server will also be on a separete network so that applications could 
access your version control server without being on the same network.

Any changes to the GitLab configuration can be done using Salt pillars on the Salt 
master.

Requirements
============
* A Heat provider that supports the following:
  * OS::Neutron::Net
  * OS::Neutron::Subnet
  * OS::Neutron::Router
  * OS::Neutron::RouterInterface
  * OS::Neutron::FloatingIP
  * OS::Neutron::FloatingIPAssociation
  * OS::Neutron::Port
  * OS::Heat::SoftwareConfig
  * OS::Heat::SoftwareDeployment
  * OS::Heat::RandomString
  * OS::Heat::ResourceGroup
  * OS::Nova::Server
  * OS::Nova::KeyPair

* A separete application network where GitLab node will be connected.
* The flavor should have at least 10GB of disk space.
* An Ubuntu image (12.04) preconfigured with heat-cfntools and heat confog-script. 
Instructions for creating a heat-cfntools enabled image for use with Heat can be 
found [here] (http://docs.openstack.org/developer/heat/getting_started/jeos_building.html).

* An OpenStack username, password, and tenant id.
* [python-heatclient](https://github.com/openstack/python-heatclient)
`>= v0.2.12`:

```bash
pip install python-heatclient
```
Using Heat-client 
=============
Here is an example of how to deploy this template using the
[python-heatclient](https://github.com/openstack/python-heatclient):

```
heat stack-create hadoop-stack -f gitlab_stack.yaml \
  -e env.yaml -P flavor=m1.large;floating-network-id=<NET_ID>; \
   < ... > ;image=<IMAGE_ID>
```
All the required parameters are as follows:
```
    "image": Image name
    "floating-network-id": UUID of the external network. The private network created by this stack will route to this network. Also, any floating ip's needed by this stack will come this network.
    "keyname": Key name for loggin in to instances
    "flavor": Flavor of GitLab instance
    "gitlab-from-email": GitLab "from" email
    "gitlab-hostname": Hostname for GitLab server

```

Using Salt
=============
If you wish to make changes to your environment after all the nodes were built, 
you may re-run salt states as follows:

```
salt -G 'roles:gitlab' state.highstate
```
The roles work as follows: 
"gitlab" is a role for setting up gitlab servers.

Using Horizon
=============
You can also go to your Horizon Dashboard in your browser and create 
the GitLab stack from under the Orchestration tab.

License
=======
```
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
