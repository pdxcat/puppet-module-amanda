# Amanda module for Puppet

## Description
Provides amanda server and client configuration

## Usage:

<pre>
  node "backup.cat.pdx.edu" {
    class { "amanda::server":
      configs => [ "rolling", "archive" ],
      owner   => "amanda",
      group   => "amanda",
    }
  }

  node "client.cat.pdx.edu" {
    class { "amanda::client":
      server => "backup.cat.pdx.edu",
    }
  }
</pre>

### More control/specificity:
<pre>
  node "backup.cat.pdx.edu" {
    file { "/etc/amanda":
      ensure => directory;
    }

    amanda::config { "rolling":
      ensure  => "present",
      confdir => "/etc/amanda";
    }
  }
</pre>

Then place your config files in the "files" directory of the module:

  1. master: $modulepath/amanda/files/server/<namevar>/*

The contents of the <namevar> directory will be synced to the confdir you
specify in the amanda::config resource. If you want to keep your config
files in a seperate puppet module, then specify the module when you
declare the server class:

<pre>
  node "backup.cat.pdx.edu" {
    class { "amanda::server":
      configs       => [ "rolling", "archive" ],
      confdir       => "/etc/amanda",
      owner         => "amanda",
      group         => "amanda",
      confsrcmodule => "secrets",
      confsrcroot   => "amanda",
    }
  }
</pre>

In this example, config files will be pulled from:

  1. master: $modulepath/secrets/files/amanda/rolling/*
  2. master: $modulepath/secrets/files/amanda/archive/*

And synced to:

  1. agent: /etc/amanda/rolling/*
  2. agent: /etc/amanda/archive/*
