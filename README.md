# JGriffiths - July 2016

## Issues

* Entire /opt/apache directory is mode 0777.
* Some /opt/apache directories are owned by uid/gid 500. This user is not in /etc/passwd.
* Apache is operating as root. This is configured in httpd.conf and is allowed to happen as apache was configured with '-DBIG\_SECURITY\_HOLE'.
* The Apache version is majorly behind even the latest legacy (2.2) branch. There have been numerous bug fixes and security patches since then.
* The init script for Apache is using sysvinit instead of Upstart.
* All modules have been statically compiled into the httpd binary including many which are not required. Using mod\_so and LoadModule for shared modules is more manageable and allows for performance improvements in some areas.
* There appears to be no obvious build process for the current Apache version.

## Assumptions

* All webservers are using Ubuntu Precise (12.04)
* There is no existing policy requiring the usage of this particular build of Apache
* There is an existing firewall on the network (thus use of netfilter is not necessary)
* The predominant proportion of configuration changes are made to vhosts; not to global httpd configuration.

## Suggestions

* Remove the entire /opt/apache directory.
* Install a legacy 2.2 Apache2 binary distribution from the supported repositories. This also has 5y support from Canonical.
* Install any required modules as binary shared objects from the supported repositories.
* Enable and disable modules as required by the particular (proxy) application.
* Add vhosts to /etc/apache2/sites-available and enable as appropriate (symlink to /etc/apache2/sites-enabled.

## Implementation

* Ansible implementation to take the host details with a number of arguments to set up/update webservers.

## Usage

* Install ansible using `pip install --upgrade ansible`, ensuring that ansible>=2.1 is installed
* Add or remove hosts by editing the file ./ansible/hosts
* Edit httpd/virtualhost configurations in ./ansible/roles/webserver/templates, using the Jinja2 format
* Run all setup/configuration from the ./ansible directory (e.g. cd ./ansible) with `ansible-playbook -i ./hosts -l webservers ./webserver.yml`
* The tasks can be limited to one of the below by adding `--tags=...` to the above command.
  * cleanup: removes existing apache installation (/opt/apache)
  * install: installs new apache package and modules
  * upgrade: upgrades apache and module packages
  * update: updates the apache/virtualhost configuration
