Redmine on OpenShift
=========================

Redmine is a flexible project management web application. Written using Ruby on Rails framework, it is cross-platform and cross-database.

Redmine is open source and released under the terms of the GNU General Public License v2 (GPL).

More information can be found at www.redmine.org

Running on OpenShift
--------------------

Create an account at https://www.openshift.com

Create a ruby application with either MySQL

	rhc app create redmine ruby-2.0 mysql-5.5 

or PostgreSQL

	rhc app create redmine ruby-2.0 postgresql-9.2

Make a note of the username, password, and host name as you will need to use these to login to the database.


Add this upstream Redmine quickstart repo

	cd redmine
	git remote add upstream -m master git://github.com/openshift/openshift-redmine-quickstart.git
	git pull -s recursive -X theirs upstream master

Then push the repo upstream

	git push

That's it, you can now checkout your application at:

	http://redmine-$yournamespace.rhcloud.com


Use the following to login to your new Redmine application running on OpenShift:

	username: admin
	password: admin


Changing the default admin password
-----------------------------------
Once your installation is complete, it is highly recommended that you change
the password for the Redmine admin user - see the Change password link at:

	http://redmine-$yournamespace.rhcloud.com/my/account

Procedure create this instance redmine
-----------------------------------
<pre>
# The Openshift application must be configured as follows:
# Ruby 1.9 or 2.0 (no scaling)
# MySql 5.1

# Begin the installation by navigating to the app runtime directory
cd ~/app-root/runtime/repo/

# delete all
rm -rf *

# download redmine
wget http://www.redmine.org/releases/redmine-3.1.2.tar.gz
tar xvzf redmine-3.1.2.tar.gz
rm redmine-3.1.2.tar.gz

# extract it to its new environment
mv redmine-3.1.2/* ~/app-root/runtime/repo/
rm -rf redmine-3.1.2*

# install bundeler for your version ruby
gem install bundler -no-ri -no-rdoc
#bug openshift, bundler working for old version ruby, you should now restart session SSH 


# get the configuration files
cd ~/app-root/runtime/repo/config
wget —no-check-certificate https://raw.githubusercontent.com/GregorianPL/openshift-redmine-3.1.2-quickstart/master/config/database.yml-2.0
wget —no-check-certificate https://raw.githubusercontent.com/GregorianPL/openshift-redmine-3.1.2-quickstart/master/config/configuration.yml
cd ~/app-root/runtime/repo/

# bundle install
bundle install --no-deployment 

# populate the database
rake generate_secret_token
RAILS_ENV=production rake db:migrate
RAILS_ENV=production rake redmine:load_default_data

# restart the app/gear
gear stop
gear start
</pre>


Version
-----------------------------------
Redmine 3.1.2
