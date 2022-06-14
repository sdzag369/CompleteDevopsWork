## why do we use puppet

> puppet is configuration management tool that helps system administrators to automate the provisioning configuration and managment of a server infrastructure. puppet enables system administrators and DevOps to work faster and smarter. 

> puppet works with manifest files in which we define the resources that helps to make configuration changes on remote servers. puppet discovers the system information via a utility called Facter and compiles the puppet manifests into a system-specific catalog containing resources & resource dependency, which are applied against the target systems. any actions taken by puppet are then reported. 

## PUPPET MASTER:

> puppet master is a service runs a the centralized or main server which used to manage the entier clients to deploy, configure and maintains the infrastructure.

## PUPPET AGENT: 

> puppet agent is a service that run on the client which sends the request to master & gets the catalog of changes from master and applies them on server where it is running. it finds any resource that is not in their desired state & makes changes as neccessary to correct them. after applying the catalog the agent submits a report to the puppet master. 

## CATALOG:

> a catalog is a document that describes the desired system state for one specific server. it lists all the resources that need to be managed as well as dependencies between those resources. 

## MANIFESTS: 

> manifests are files with extension ".pp" where we declare all resources to be checked or to be changed. resources may be files, packages, services etc....

## RESOURCES ARE TWO  TYPES
```
1) A type ( package, service, user, mount, exec etc... )
2) A title ( how the resource types are called and reffered )
```
```
Sample syntax: 
resourcetype{'resourcetitle':
	argument1 => 'value',
	argument2 => 'value',
}
```

## CLASSES

> classes are containers or groups of different resources. 

> note that when we define a class we just describe what it does and what parameters it has we dont actually add its resources to the catalog.

```
calss myclass {
	# execute 'apt-get update'
	exec { 'apt-update':                    # exec resource named 'apt-update'
		command => '/usr/bin/apt-get update'  # command this resource will run
	}

	package { 'vim':
		require => Exec['apt-update'],        # require 'apt-update' before installing
		ensure => installed,
	}
	
	# ensure sshd service is running
	service { 'sshd':
		ensure => running,
	}
}
```


> Manifests are files with extension ".pp" will be created under /etc/puppet/manifests directory at the puppet master end, where we declare all the resources types status to be managed

```
resources might be files,packages, services etc...

example scenario
requirements are: 
	1) modify an existing file
	2) create a new config file


Syntax: 
resourcetype{'title':
	argument or attribute => value,
	argument or attribute => value,
}

example: 
service{'crond':
	ensure => 'stopped',
	enable => 'false',
}
```
```
HELP: 
puppet resource --help
puppet resource --types
puppet describe file
puppet describe --list
```

## what are puppet modules ? why do we need puppet modules ? 

> do we realy require programming lang for writing puppet modules ?

> what are pre-existing puppet modules ?

> A module is a collection of manifests and data ( such as facts, files, and templates ), and they have a specific directory structure. Modules are useful for organizing your puppet code, because they allow you to split your code into multiple manifests. it is considered the best practice to use modules to organize almost all of your puppet manifests. 

```
for example, to install and manage apache webserver, we write a puppet code in a single manifest file. 
in modules, we split these operations into multiple manifests file and declare that as a classes using "include statement"

example:
node "nodename" {
	include apache_install
	inclue apache_restart 
}
```

> by implementing puppet modules, out manifest file can become much smaller, more readable. 

> for creating puppet modules, you need basic understanding of the puppet language to become more familier with puppet modules, like what is classes, class declarations, class definitions etc..

> writing puppet modules can be easier to those who has enough programming knowledge to develop the codes for modules


> how about others who doesn't have programming knowledge ? can use the pre-existing puppet modules. available in puppet forge & github sites

> pre-existing modules are written by the community for open source puppet and puppet enterprise IT automation software. these are available publicly as a repository. you can use these puppet modules to your infrastructure by automating tasks such as setting up webserver, mailserver etc...

> we have over 4000+ modules available on puppet Forge & github. we can use all of then straight away. if you are good enough to write puppet modules, you can also contribute to puppet community. 

```
puppet module list
puppet module search nano
puppet module list
puppet module install nano

/opt/puppetlabs/bin/puppet module generate naresh-tomcat 
/opt/puppetlabs/bin/puppet apply --modulepath=<path> <modulename>/tests/init.pp --noop 
```

## Nodes - Default classification

> A node is identified by the PuppetMaster by its certname, which defaults to the node's fqdn

> In the first manifest file parsed by the Master, site.pp, we can define nodes with a syntax like:

```
node 'web01' {
  include apache
}
We can also define a list of matching names:

node 'web01' , 'web02' , 'web03' {
  include apache
}
or use a regular expression:

node /^www\d+$/ {
  include apache
}

```

## Useful paths

```
/var/log/puppet contains logs (but also on normal syslog files, with facility daemon), both for agents and master

/var/lib/puppet contains Puppet operational data (catalog, certs, backup of files...)

/var/lib/puppet/ssl contains SSL certificate

/var/lib/puppet/clientbucket contains backup copies of the files changed by Puppet

/etc/puppet/manifests/site.pp (On Master) The first manifest that the master parses when a client connects in order to produce the configuration to apply to it (Default on Puppet < 3.6 where are used config-file environments)

/etc/puppet/environments/production/manifests/site.pp (On Master) The first manifest that the master parses when using directory environments (recommended from Puppet 3.6 and default on Puppt >= 4)

/etc/puppet/modules and /usr/share/puppet/modules (On Master) The default directories where modules are searched

/etc/puppet/environments/production/modules (On Master) An extra place where modules are looked for when using directory environments

/var/lib/puppet/reports -- To see the reports reported by nodes
```
