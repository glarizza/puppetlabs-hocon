#HOCON file

####Table of Contents

1. [Overview](#overview)
2. [Module Description - What the module does and why it is useful](#module-description)
3. [Setup - The basics of getting started with the hocon module](#setup)
    * [Setup requirements](#setup-requirements)
    * [Beginning with hocon](#beginning-with-hocon)
4. [Usage - Configuration options and additional functionality](#usage)
5. [Reference - An under-the-hood peek at what the module is doing and how](#reference)
6. [Development - Guide for contributing to the module](#development)

##Overview 

This module adds resource types to manage settings in HOCON-style configuration files.

##Module Description

The hocon module adds a resource type so that you can use Puppet to manage settings in HOCON configuration files.

##Setup

##Beginning with hocon

To manage a HOCON file, add the resource type `hocon_setting` to a class.

##Usage

Manage individual settings in HOCON files by adding the `hocon_setting` resource type to a class. For example:

```
hocon_setting { "sample setting":
  ensure  => present,
  path    => '/tmp/foo.conf',
  setting => 'foosetting',
  value   => 'FOO!',
}
```

To control a setting nested within a map contained at another setting, provide the path to that setting
under the "setting" parameter, with each level separated by a ".". So to manage `barsetting` in the following map

```
foo {
    bar {
        barsetting="FOO!"
    }
}
```

You would put the following in your manifest:

```
hocon_setting {'sample nested setting':
  ensure  => present,
  path => '/tmp/foo.conf',
  setting => 'foo.bar.barsetting',
  value   => 'BAR!',
}
```

You can also set maps like so:

```
hocon_setting { 'sample map setting':
  ensure => present,
  path => '/tmp/foo.conf',
  setting => 'hash_setting',
  value => { 'a' => 'b' },
}
```

##Reference

###Type: hocon_setting

#### Parameters

* `ensure`: Ensures that the resource is present. Valid values are 'present', 'absent'.

* `name`: An arbitrary name used as the identity of the resource.

* `path`: The HOCON file in which Puppet will ensure the specified setting.

* `provider`: The specific backend to use for this `hocon_setting` resource. You will seldom need to specify this --- Puppet will usually discover the appropriate provider for your platform. The only available provider for `hocon_setting` is ruby.

* `setting`: The name of the HOCON file setting to be defined. This can be a top-level setting or a setting nested
  within another setting. To define a nested setting, give the full path to that setting with each level separated
  by a `.` So, to define a setting `foosetting` nested within a setting called `foo` contained on the top level,
  the `setting` parameter would be set to `foo.foosetting`.

* `value`: The value of the HOCON file setting to be defined.

* `type`: The type of the value passed into the `value` parameter. This value should be a string, with valid values being
    `'number'`, `'boolean'`, `'string'`, `'hash'`, `'array'`, and `'text'`.
    
    This parameter will not be need to be set most of the time, as the module
    is generally smart enough to figure this out on its own. There are only two cases in which this parameter is required.
    
    The first is the case in which the `value` type is a single-element array. In that case, the `type` parameter will need to be set to
    `'array'`. So, for example, to add a single-element array, you would add the following to your manifest
    
    ```
    hocon_setting { 'single array setting':
      ensure => present,
      path => '/tmp/foo.conf',
      setting => 'foo',
      value => [1],
      type => 'array',
    }
    ```
    
    Since this type represents a setting in a configuration file, you can pass a string containing the exact text of the value as you want it to appear
    in the file (this is useful, for example, if you want to set a parameter to a map or an array but want comments or specific indentation on elements in the map/array).
    In this case, `value` must be a string with no leading or trailing whitespace, newlines, or comments that contains a valid HOCON value, and the
    `type` parameter must be set to `'text'`. This is an advanced use case, and will not be necessary for most users. So, for example, say you want to
    add a map with particular indentation/comments into your configuration file at path `foo.bar`. You could create a variable like so
    
    ```
    $map = 
    "{
        # This is setting a
        a : b
        # This is setting c
            c : d
     }"
    ```
    
    And your configuration file looks like so
    
    ```
    baz : qux
    foo {
      a : b
    }
    ```
    
    You could then write the following in your manifest
    
    ```
    hocon_setting { 'exact text setting':
      ensure => present,
      path => '/tmp/foo.conf',
      setting => 'foo.bar',
      value => $map,
      type => 'text',
    }
    ```
    
    And the resulting configuration file would look like so
    
    ```
    baz : qux
    foo {
      a : b
      bar : {
          # This is setting a
          a : b
          # This is setting c
              c : d
      }
    }
    ```
    
    Aside from these two cases, the `type` parameter does not need to be set.

##Development
 
Puppet Labs modules on the Puppet Forge are open projects, and community contributions are essential for keeping them great. We can’t access the huge number of platforms and myriad of hardware, software, and deployment configurations that Puppet is intended to serve.

We want to keep it as easy as possible to contribute changes so that our modules work in your environment. There are a few guidelines that we need contributors to follow so that we can have a chance of keeping on top of things.

You can read the complete module contribution guide on the [Puppet Labs wiki](http://projects.puppetlabs.com/projects/module-site/wiki/Module_contributing).

##Contributors

The list of contributors can be found at: [https://github.com/puppetlabs/puppetlabs-hocon/graphs/contributors/contributors](https://github.com/puppetlabs/puppetlabs-hocon/graphs/contributors/contributors).
