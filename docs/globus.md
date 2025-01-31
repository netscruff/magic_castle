# Globus Endpoint in Magic Castle

A Globus Endpoint can be configured automatically for a Magic Castle cluster.
There are however a few manual steps to accomplish.

## Requirements
### Create a Globus ID Account

We will store your Globus credentials in a YAML file on the cluster in
clear. While the file is only readable by root, if you prefer to not use your
personal globus, we recommend you create a dummy one at:

[https://www.globusid.org/create](https://www.globusid.org/create)

### DNS

If your cluster hostname is not registered in the domain records of your DNS,
you will have to register it manually. Make sure you use the same domain name
as the one you provided in the Terraform main file.

## Setup

On `mgmt01`:
1. Edit the file `/etc/puppetlabs/code/environments/production/data/common.yaml` with sudo rights
and add the following lines:
  ```
profile::globus::base::globus_user: your_globus_username
profile::globus::base::globus_password: your_globus_password
```
Replace `your_globus_username` and `your_globus_password` by their respective value.

On `login01`:
1. Restart puppet : `sudo systemctl restart puppet`.
2. Give Puppet a few minutes to setup Globus. You can confirm that
everything was setup correctly by looking at the tail of the puppet log:
```
sudo journalctl -u puppet -f
```
3. If everything is correct, `globus-gridftp-server` and `myproxy-server`
services should be active. To confirm:
```
sudo systemctl status globus-gridftp-server
sudo systemctl status myproxy-server
```
4. If both services are active, in a browser, go to
https://app.globus.org/endpoints?scope=administered-by-me
5. Click on your endpoint.
6. Click on the `Server` tab.
7. At the bottom of the page, there is text field named "Subject DN". Copy the content, it should look like this:
```
/C=US/O=Globus Consortium/OU=Globus Connect Service/CN=xxxx-x-xxx-xxx-xxxxxx
```
8. The first section of the Server page is named "Identity Provider". Click on the `Edit Identity Provider` button.
9. In the DN field of Edit Identity Provider, paste the content you copied from "Subject DN".
10. Click on Save Changes.

You should now be able to log in your Globus Endpoint with your cluster guest account credentials.