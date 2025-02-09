
******************************
Installation and configuration
******************************

=============
Installation
=============

This package installs just like any other Python package.
It is a pure Python package and works with Python 3.x
installations.  To install the latest version using `pip`, you execute the following::

    pip install saspy

or, for a specific release::

    pip install http://github.com/sassoftware/saspy/releases/saspy-X.X.X.tar.gz

or, for a given branch (put the name of the branch after @)::

    pip install git+https://git@github.com/sassoftware/saspy.git@branchname

To use this module after installation, you need to edit the sascfg.py file to 
configure it to be able to connect and start a SAS session. Note, you should 
actually copy sascfg.py to sascfg_personal.py and edit sascfg_personal.py.
This way your edit's won't be overridden if a new sascfg.py is pulled.
Follow the instructions in the next section.

* If you run into any problems, see :doc:`troubleshooting`.
* If you have questions, open an issue at https://github.com/sassoftware/saspy/issues.



===============
Configuration
===============

This module can connect and start different kinds of SAS sessions. It can connect to SAS 
on Unix, Mainframe, and Windows. It can connect to a local SAS session or remote session.
Because of the wide range of connection types, there are a number of different access methods
which are used to connect to different kinds of SAS sessions.

The current set of connection methods are as follows:

`STDIO`_
  This connection method is available on the Linux platform only. This 
  method enables you to connect to SAS on the same host as your Python process.

`STDIO over SSH`_
  This connection method is also available on the Linux platform only. This
  method can connect to SAS that is installed on a remote host, if you have passwordless
  SSH configured for your Linux user account.

`IOM using Java`_
  The integrated object method (IOM) connection method supports SAS on any platform.
  This method can make a local Windows connection and it is also the way to connect 
  to SAS Grid through SAS Grid Manager. This method can connect to a SAS Workspace
  Server on any supported SAS platform.

`IOM using COM`_ 
  This connection method is for Windows clients connecting to a remote SAS 9.4 host. This
  method takes advantage of the IOM access method, but does not require a Java dependency.
  SAS Enterprise Guide or SAS Integration Technologies Client (a free download from SAS Support)
  is required to install the SAS COM library on your client system.
    
`HTTP`_
  This access mehtod uses http[s] to connect to the Compute Service (a micro service) of a Viya
  instalation. This does not connet to SAS 9.4 via http. The Compute Service will start a
  Compute Server using the SPRE image of MVA SAS that is installed in the Viya deployment.
  This is roughly equivalent to a Workspace server via IOM, but in Viya with no SAS 9.4.

Though there are several connection methods available, a single configuration file
can be used to enable all the connection methods. The sample config file contains instructions and
examples, but this section goes into more detail to explain how to configure each
type of connection.

Depending upon how you did your installation, the sample sascfg.py file may be in different 
locations on the file system:

* In a regular pip install, it is under the site-packages directory in the Python 
  installation. 
* If you cloned the repo or downloaded and extraced the repo to some directory and then installed, 
  it will be in that directory and maybe also copied to site-packages.
 
If you are not sure where to look, then there is a very simple way to determine the location
of your saspy installation.

After installing, start Python and ``import saspy``. Then, simply submit ``saspy``. 
Python will show you where it found the module (it will show the __init__.py file in that directory).
It is the directory that is the module, so the sascfg.py file is in that directory, same as __init__.py.

.. code-block:: ipython3

    # this is an example of a repo install on Windows:

    C:\>python
    Python 3.6.0 |Anaconda custom (64-bit)| (default, Dec 23 2016, 11:57:41) [MSC v.1900 64 bit (AMD64)] on win32
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import saspy
    >>> saspy
    <module 'saspy' from 'C:\\ProgramData\\Anaconda3\\lib\\site-packages\\saspy\\__init__.py'>
    >>>
    So sascfg.py is: C:\ProgramData\Anaconda3\lib\site-packages\saspy\sascfg.py 


    # this is an example of a repo install on Linux:

    Linux-1> python3.5
    Python 3.5.1 (default, Jan 19 2016, 21:32:20)
    [GCC 4.4.7 20120313 (Red Hat 4.4.7-16)] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import saspy
    >>> saspy
    <module 'saspy' from '/opt/tom/github/saspy/saspy/__init__.py'>
    >>>
    So sascfg.py is: /opt/tom/github/saspy/saspy/sascfg.py


    # this is an example of a PyPi install on Linux into site-packages:

    Linux-1> python3.5
    Python 3.5.1 (default, Jan 19 2016, 21:32:20)
    [GCC 4.4.7 20120313 (Red Hat 4.4.7-16)] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import saspy
    >>> saspy
    <module 'saspy' from '/usr/lib/python3.5/site-packages/saspy/__init__.py'>
    >>>
    So sascfg.py is: /usr/lib/python3.5/site-packages/saspy/sascfg.py


sascfg_personal.py
==================

Originally, sascfg.py was the config file saspy used. But, since the saspy.cfg file is in the saspy repo, it can be updated
on occasion and when you do an upgrade it will pull down the repo sascfg.py and replace the one
you've in your instalation. If you used that file for your configuration, then you would need to keep
a copy elsewhere and then replace the new one with your copy after upgrading or pulling, if yours was replaced. 

There is a simple solution to this. Your configurations can (should) be in a file named sascfg_personal.py.
This file doesn't exist in the repo, so it will never be overwritten when you upgrade or pull.
saspy will always try to import sascfg_personal.py first, and only if that fails will it try to
import sascfg.py.

So copy sascfg.py to sascfg_personal.py and put all of your specific configuration into the _personal
file. Then you won't have to worry about sascfg.py getting clobbered when you pull or upgrade. Note that
the sascfg.py file has examples of all of the various kinds of connections you could use. You don't need
all of that in your _personal version; only the parts you need for your situation. The next section
explains the minimum parts you would need.

Also, everything in this doc applies to the _personal version; it's the same, just a version of the file
that will be used if it exists instead of the original one, but it won't get overwritten.

Also note that this file does not have to live in the repo itself. It can be anywhere on the filesystem
as long as that location is accessible to python. If the path is in the python search path, then your good.
That includes being in the repo directory, of course, which is the most convenient (that's where I have it!).

If it is in the repo or another path that python will find it, you can just create a session as follows:

.. code-block:: ipython3

    sas = saspy.SASsession()


If, however, it is not in the python search path, you can use the cfgfile= parameter in your SASsession() invocation to 
specify its location:

.. code-block:: ipython3

    sas = saspy.SASsession(cfgfile='/some/path/to/your/config/sascfg_personal.py')


The python search path can be found by looking at the PYTHONPATH environment variable (if it's set), 
but more definitively by submitting the following:

.. code-block:: ipython3

    import sys
    sys.path

        
sascfg_personal.py (saspy_personal.py) details
==============================================
There are three main parts to this configuration file.

        1) SAS_config_names
        2) SAS_config_options
        3) Configuration definitions

In reverse order, the configuration definitions are Python dictionaries. Each dictionary 
has the settings for one connection method (STDIO, SSH, IOM, and so on) to a SAS session.
These values are defined in the following sections.

SAS_config_options has two options. The first option (lock_down) restricts (or allows) an end
users' ability to override settings in the configuration definitions by passing them as parameters
on the ``SASsession()``. Each of the keys in the configuration definition can be passed in at
run time on the SASsession(). If lock_down is set to True, any keys defined in the configuration
definition cannot be overridden in SASsession(), Keys that are not specified in the Config Def, can be
specified at run time on the SASsession(). If set to False, any config def key can be specified 
on the SASsession(). 

The second (verbose) controls the printing of some debug type messages.

SAS_config_names is the list of configuration definition names to make available to an
end user at connection time. Any configuration definitions that are not listed in 
SAS_config_names are simply inaccessible by an end user. You can add several configuration
definitions in the file but not make them available by simply excluding the names from 
the list. Also note that these names can be anything you want. The names of the example
configuration definitions we chosen to be self-documenting. There nothing special about 'winlocal',
it could be named Bob. But then it wouldn't be obvious that it's for a WINdows install running a LOCAL copy of SAS.


So, your sascfg_personal.py file only need a few things in it; not everything in the example sascfg.py file.
For example, if you had SAS installed on your Linux system, your sascfg_personal.py file may simply be the following:

.. code-block:: ipython3

    SAS_config_names   = ['mycfg']
    SAS_config_options = {'lock_down': False,
                          'verbose'  : True
                         }
    mycfg              = {'saspath'  : '/opt/sasinside/SASHome/SASFoundation/9.4/bin/sas_u8'
                         }



STDIO
=====
This is the original access method. This works with Unix only,
because SAS on Windows platforms does not support line-mode style connections
(through stdin, stdout, stderr). This connection method is for a local 
connection to SAS that is installed on the same host as Python.

There are only four keys for this configuration definition dictionary:

saspath - 
    (Required) Path to SAS startup script

options -
    SAS options to include in the start up command line. These **must** be a
    Python list.

encoding -
    NOTE: as of saspy V2.4.2, you no longer need to set the encoding. SASpy
    will determine the SAS session encoding and map that to the Python encoding for you.

    This is the Python encoding value that matches the SAS session encoding
    of the SAS session to which you are connecting. The Python encoding 
    values can be found at `encodings-and-unicode <https://docs.python.org/
    3.5/library/codecs.html#encodings-and-unicode>`_.
    The three most common SAS encodings, UTF8, LATIN1, and WLATIN1 are the 
    default encodings for running SAS in Unicode, on Unix, and on Windows,
    respectively. Those map to Python encoding values: utf8, latin1, and
    windows-1252, respectively. 

autoexec -
    This is a string of SAS code that will be submitted upon establishing a connection.
    You can use this to preassign libraries you always want available, or whatever you want.
    Don't confuse this with the autoexec option of SAS which specifies a sas program file to be run.
    That is different. This is a string of SAS code saspy will submit after the session is created,
    which would be after SAS already included any autoexec file if there was one.

lrecl -
    An integer specifying the record length for transferring wide data sets from SAS to Data Frames.

display -
    This is a new key to support Zeppelin (saspy V2.4.4). The values can be either 'jupyter' or 'zeppelin'. The default
    when this is not specified is 'jupyter'. Jupyter uses IPython to render HTML, which is how saspy has 
    always worked. To support Zeppelin's display method, a different display interface had to be added to saspy.
    If you want to run saspy in Zeppelin, set this in your configuration definition: 'display' : 'zeppelin', 

.. code-block:: ipython3

    default  = {'saspath': '/opt/sasinside/SASHome/SASFoundation/9.4/bin/sas_u8',
                'options' : ["-fullstimer", "-autoexec", "/user/tom/autoexec.sas"],
                'autoexec': "libname mylib 'some/library/to/pre-assign';"
                }

.. note:: The trigger to use the STDIO connection method is the absence of any
          trigger for the other access methods: not having ``'ssh'`` or ``'java'``
          keys in the configuration definition.


STDIO over SSH
==============
This is the remote version of the original connection method. This also works 
with Unix only, and it supports passwordless SSH to the Unix machine where SAS
is installed. It is up to you to make sure that user accounts have passwordless
SSH configured between the two systems. Google it, it's not that difficult.

If you don't already have this set up, you need to generate rsa keys. Starting
after version 2.2.9, you can specify an identity file (.pem file) instead by
providing the file path on the identity key. Either of these provide passwordless access.
If you have any trouble with this, you will find that adding -vvv to the command saspy
trys to run (run that yourself from a shell with -vvv added) will provide significant
diagnostics about how ssh is trying to authenticate. Something like the following:

/usr/bin/ssh -vvv hostname.to.connect.to 

In addition to the keys for STDIO, there are two more keys to configure:

ssh - 
    (Required) The ssh command to run (Linux execv requires a fully qualified
    path. Even if the command is found in the PATH variable, it won't be used.
    Enter the fully qualified path.)

host - 
    (Required) The host to connect to. Enter a resolvable host name or IP address.

.. code-block:: ipython3

    ssh      = {'saspath' : '/opt/sasinside/SASHome/SASFoundation/9.4/bin/sas_u8',
                'ssh'     : '/usr/bin/ssh',
                'host'    : 'remote.linux.host',
                'options' : ["-fullstimer"]
               }

.. note:: The ``'ssh'`` key is the trigger to use the STDIO over SSH connection
          method.

To accomodate alternative SSH configurations, you may also provide any of the 
following optional keys:

identity -
    (Optional: string) The path to the identity file to use. A .pem file.

port -
    (Optional: integer) The ssh port of the remote machine (equivalent to invoking ssh with the ``-p`` option)

tunnel -
    (Optional: integer) Certain methods of saspy require opening a local port and accepting a connection and data 
    streamed from the SAS instance to saspy. If the remote SAS server would not be able to reach ports on your client machine 
    due to a firewall or other security configuration, you may pass a port number to used for SAS to connect to on
    the remote side, which will be forwarded to the local side (using the ``-R`` ssh option) so that the remote SAS
    server can connect using this port.

rtunnel -
    (Optional: integer) Certain methods of saspy require opening a remote port and allowing a connection to be made and 
    data streamed to the SAS server from saspy; the Reverse of the tunnel case. In these cases, saspy needs to provide
    a port for the SAS server to use to accept a connection so data can be streamed to the SAs server.
    This is simply the reverse of the tunnel case, where SAS creates the socket and saspy connects. This will use
    the ``-L`` ssh option so that the saspy can connect to the remote SAS server on this port.


.. code-block:: ipython3

    ssh      = {'saspath' : '/opt/sasinside/SASHome/SASFoundation/9.4/bin/sas_u8',
                'ssh'     : '/usr/bin/ssh',
                'host'    : 'remote.linux.host',
                'identity': '/usr/home/.ssh/alt_id.pem',
                'port'    : 9922,
                'tunnel'  : 9911
                'rtunnel' : 9912
               }


IOM using Java
==============
This connection method opens many connectivity options. This method enables you to 
connect to any Workspace server on any supported platform. 

You can also use `SAS Grid Manager <https://www.sas.com/en_us/software/foundation/grid-manager.html>`__
to connect to a SAS grid. This method, compared to STDIO over SSH, enables SAS Grid
Manager to control the distribution of connections to the various grid nodes
and integrates all the monitoring and administration that SAS Grid Manager provides.

The IOM connection method also enables you to connect to SAS from Windows (STDIO was Linux only).
The connection can be to a local SAS installation or a remote IOM Workspace server running
on any supported platform.

The IOM connection method requires the following:

* Java 7 or higher installed on your Client machine (where you're running SASPy)
* The SAS Java IOM Client (just the jars listed below; these can be copied to your client system from wherever your SAS install is)
* Setting the CLASSPATH to access the SAS Java IOM Client JAR files.
* Setting the CLASSPATH to include the the saspyiom.jar file (and the thirdparty jars for Java version 9 and higher).
* Setting the CLASSPATH to include client side encryption jars, if you have encryption configured for your IOM

The ``'classpath'`` key for the configuration definition requires a little additional
explanation before we get to further details. There are four (4) JAR files that are 
required for the Java IOM Client. The JAR files are available from your existing SAS
installation.  There is one JAR file that is provided with this package: 
saspyiom.jar. These five JAR files must be provided (fully qualified paths) in a 
CLASSPATH environment variable. This is done in a very simple way in the sascfg_personal.py 
file, like so:

::

    # build out a local classpath variable to use below for Linux clients  CHANGE THE PATHS TO BE CORRECT FOR YOUR INSTALLATION
    cp  =  "C:\\Program Files\\SASHome\\SASDeploymentManager\\9.4\\products\\deploywiz__94472__prt__xx__sp0__1\\deploywiz\\sas.svc.connection.jar"
    cp += ";C:\\Program Files\\SASHome\\SASDeploymentManager\\9.4\\products\\deploywiz__94472__prt__xx__sp0__1\\deploywiz\\log4j.jar"
    cp += ";C:\\Program Files\\SASHome\\SASDeploymentManager\\9.4\\products\\deploywiz__94472__prt__xx__sp0__1\\deploywiz\\sas.security.sspi.jar"
    cp += ";C:\\Program Files\\SASHome\\SASDeploymentManager\\9.4\\products\\deploywiz__94472__prt__xx__sp0__1\\deploywiz\\sas.core.jar"
    cp += ";C:\\ProgramData\\Anaconda3\\Lib\\site-packages\\saspy\\java\\saspyiom.jar"

    # And, if you've configured IOM to use Encryption, you need these client side jars.
    cp += ";C:\\Program Files\\SASHome\\SASVersionedJarRepository\\eclipse\\plugins\\sas.rutil_904300.0.0.20150204190000_v940m3\\sas.rutil.jar"
    cp += ";C:\\Program Files\\SASHome\\SASVersionedJarRepository\\eclipse\\plugins\\sas.rutil.nls_904300.0.0.20150204190000_v940m3\\sas.rutil.nls.jar"
    cp += ";C:\\Program Files\\SASHome\\SASVersionedJarRepository\\eclipse\\plugins\\sastpj.rutil_6.1.0.0_SAS_20121211183517\\sastpj.rutil.jar"
    

    # Java 10+ no longer provides CORBA (9 has it but doesn't load it by default). These jars provide the CORBA support
    # that the IOM client needs. Add these to the classpath to work with the latest Java releases. 
    # These can even be in the classpath for Jave (7 or 8) that don't need them, with no problem. 
    cp += ";C:\\ProgramData\\Anaconda3\\Lib\\site-packages\\saspy\\java\\thirdparty\\glassfish-corba-internal-api.jar"
    cp += ";C:\\ProgramData\\Anaconda3\\Lib\\site-packages\\saspy\\java\\thirdparty\\glassfish-corba-omgapi.jar"
    cp += ";C:\\ProgramData\\Anaconda3\\Lib\\site-packages\\saspy\\java\\thirdparty\\glassfish-corba-orb.jar"
    cp += ";C:\\ProgramData\\Anaconda3\\Lib\\site-packages\\saspy\\java\\thirdparty\\pfl-basic.jar"
    cp += ";C:\\ProgramData\\Anaconda3\\Lib\\site-packages\\saspy\\java\\thirdparty\\pfl-tf.jar"
    

And then simply refer to the ``cp`` variable in the configuration definition:

::

    'classpath' : cp,

Also worth noting: these five JAR files are compatible with both Windows and Unix client systems. So you can copy the jars from whatever system
SAS is installed on, to your client (where python is running), even if one is Unix and the other is Windows (either way).  

.. note::
    If you have a \\u or \\U in your classpath string, like: "c:\\User\\sastpw\\...', you will have to use either 
    a double backslash instead, like \\\\u or \\\\U ("c:\\\\User\\sastpw\\...') or mark the string as raw (not 
    unicode) with a r prefix, like r"C:\\User\\sastpw\\..." 
    or else you will get an error like this: SyntaxError: (unicode error) 'unicodeescape' codec can't decode 
    bytes in position 3-4: truncated \UXXXXXXXX escape 



This following 'fix' for Java 9 is not longer needed, and it didn't solve Java 10 or 11. See the thirdparty jars above
to solve the missing CORBA in any of the Java releases.

It has been reported to me that Java9 no longer includes CORBA in it's default search path. CORBA is a requirement for
the IOM Client. This can easily be added back in using the 'javaparms' configuration key (defined below), as follows.

::

    "javaparms": ["--add-modules=java.corba"],   # NO LONGER NEEDED - See 'classpath' above for solution
  


The IOM access method now has support for getting the required user/password from an authinfo file in the user's home directory
instead of prompting for it. On linux, the file is named .authinfo and on windows, it's _authinfo. The format of the line in the authinfo file is
as follows. The first value is the authkey value you specify for `authkey`. Next is the 'user' key followed by the value (the user id)
and then 'password' key followed by its value (the user's password). Note that there are permission rules for this file. On linux the file must
have permissions of 600, only the user can read or write the file. On Windows, the file should be equally locked down to where only the owner
can read and write it.  

::

    authkey user omr_user_id password omr_user_password

So, for a Configuration Definition that specifies the following authkey:

::

    'authkey' : 'IOM_Prod_Grid1',

The authinfo file in the home directory for user Bob, with a password of BobsPW1 would have a line in it as follows:
 
::

    IOM_Prod_Grid1 user Bob password BobsPW1


Remote
~~~~~~
A remote connection is defined as a connection to any Workspace Server on any SAS platform 
from either a Unix or Windows client. This module does not connect to a SAS Metadata Server (OMR),
but rather connects directly to an Object Spawner to get access to a Workspace Server. If you already
access these with other SAS clients, like Enterprise Guide (EG), you may already be familiar with
connecting to OMR, but not directly to the others by host/port. There is information in the
:doc:`advanced-topics` section about using Proc iomoperate to find Object Spawners and Workspace 
Server to get values for the three keys defined below (iomhost, iomport, appserver).

The following keys are available for the configuration definition dictionary:

java    - 
    (Required) The path to the Java executable to use. For Linux, use a fully qualifed
    path. On Windows, you might be able to simply enter ``java``. If that is not successful,
    enter the fully qualified path.
iomhost - 
    (Required) The resolvable host name, or IP address to the IOM object spawner.
    New in 2.1.6; this can be a list of all the object spawners hosts if you have load balanced object spawners.
    This provides Grid HA (High Availability)
iomport - 
    (Required) The port that object spawner is listening on for workspace server connections (workspace server port - not object spawner port!).
classpath - 
    (Required) The CLASSPATH to the IOM client JAR files and saspyiom.jar. These can be wherever. Just make sure the path is correct.
    These jars work across platforms, so you can copy them from a Unix system to Windows or the other way too. Same with saspyiom.jar.
authkey -
    The keyword that starts a line in the authinfo file containing user and or password for this connection.
omruser - 
    (**Discouraged**)  The user ID is required but if this field is left blank,
    the user is **prompted** for a user ID at runtime, unless it's found in the authinfo file.
omrpw  - 
    (**Strongly discouraged**) A password is required but if this field is left
    blank, the user is **prompted** for a password at runtime, unless it's found in the authinfo file.
encoding  -
    NOTE: as of saspy V2.4.2, you no longer need to set the encoding. SASpy
    will determine the SAS session encoding and map that to the Python encoding for you.

    This is the Python encoding value that matches the SAS session encoding of 
    the IOM server to which you are connecting. The Python encoding values can be 
    found at `encodings-and-unicode <https://docs.python.org/3.5/
    library/codecs.html#encodings-and-unicode>`_.
    The three most common SAS encodings, UTF8, LATIN1, and WLATIN1 are the 
    default encodings for running SAS in Unicode, on Unix, and on Windows,
    respectively. Those map to Python encoding values: utf8, latin1, and 
    windows-1252, respectively. 
appserver -
    If you have more than one AppServer defined on OMR, then you must pass the name of the physical workspace server
    that you want to connect to, i.e.: 'SASApp - Workspace Server'. Without this the Object spawner will only try the
    first one in the list of app servers it supports.
sspi -
    New in 2.17, there is support for IWA (Integrated Windows Authentication) from a Windows client to remote IOM server.
    This is only for when your Workspace server is configured to use IWA as the authentication method, which is not the default.
    This is simply a boolean, so to use it you specify 'sspi' : True. Also, to use this, you must have the path to the
    spiauth.dll file in your System Path variable, just like is required for Local IOM connections.
    See the second paragraph under Local IOM for more on the spiauth.dll file.
autoexec -
    This is a string of SAS code that will be submitted upon establishing a connection.
    You can use this to preassign libraries you always want available, or whatever you want.
    Don't confuse this with the autoexec option of SAS which specifies a sas program file to be run.
    That is different. This is a string of SAS code saspy will submit after the session is created,
    which would be after SAS already included any autoexec file if there was one.

javaparms -
    The javaparms option allows you to specify Java command line options. These aren't generally needed, but this
    does allows for a way to specify them if something was needed.

lrecl -
    An integer specifying the record length for transferring wide data sets from SAS to Data Frames.

display -
    This is a new key to support Zeppelin (saspy V2.4.4). The values can be either 'jupyter' or 'zeppelin'. The default
    when this is not specified is 'jupyter'. Jupyter uses IPython to render HTML, which is how saspy has 
    always worked. To support Zeppelin's display method, a different display interface had to be added to saspy.
    If you want to run saspy in Zeppelin, set this in your configuration definition: 'display' : 'zeppelin', 


.. code-block:: ipython3

    # Unix client class path
    cpL  =  "/whever/I/put/these/jars/sas.svc.connection.jar"
    cpL += ":/whever/I/put/these/jars/log4j.jar"
    cpL += ":/whever/I/put/these/jars/sas.security.sspi.jar"
    cpL += ":/whever/I/put/these/jars/sas.core.jar"
    cpL += ":/whever/I/put/these/jars/saspyiom.jar"
    #cpL += ":/usr/lib/python3.5/site-packages/saspy/java/saspyiom.jar"

    # Windows client class path
    cpW  =  "C:\\wherever\\I\\put\\these\\jars\\sas.svc.connection.jar"
    cpW += ";C:\\wherever\\I\\put\\these\\jars\\log4j.jar"
    cpW += ";C:\\wherever\\I\\put\\these\\jars\\sas.security.sspi.jar"
    cpW += ";C:\\wherever\\I\\put\\these\\jars\\sas.core.jar"
    #cpW += ";C:\\wherever\\I\\put\\these\\jars\\saspyiom.jar"
    cpW += ";C:\\ProgramData\\Anaconda3\\Lib\\site-packages\\saspy\\java\\saspyiom.jar"

    # Unix client and Unix IOM server  NEW 2.1.6 - with load balanced object spawners
    iomlinux = {'java'      : '/usr/bin/java',
                'iomhost'   : ['linux.grid1.iom.host','linux.grid2.iom.host','linux.grid3.iom.host','linux.grid4.iom.host'],
                'iomport'   : 8591,
                'encoding'  : 'latin1',
                'classpath' : cpL,
                'appserver' : 'SASApp Prod - Workspace Server'
                }

    # Unix client and Windows IOM server
    iomwin   = {'java'      : '/usr/bin/java',
                'iomhost'   : 'windows.iom.host',
                'iomport'   : 8591,
                'encoding'  : 'windows-1252',
                'classpath' : cpL,
                'appserver' : 'SASApp Test - Workspace Server'
               }

    # Windows client and Unix IOM server
    winiomlinux = {'java'      : 'java',
                   'iomhost'   : 'linux.iom.host',
                   'iomport'   : 8591,
                   'encoding'  : 'latin1',
                   'classpath' : cpW
                  }

    # Windows client and Windows IOM server
    winiomwin   = {'java'      : 'java',
                   'iomhost'   : 'windows.iom.host',
                   'iomport'   : 8591,
                   'encoding'  : 'windows-1252',
                   'classpath' : cpW
                  }

    # Windows client and with IWA to Remote IOM server
    winiomIWA   = {'java'      : 'java',
                   'iomhost'   : 'some.iom.host',
                   'iomport'   : 8591,
                   'classpath' : cpW,
                   'sspi'      : True
                  }


Local
~~~~~
A local connection is defined as a connection to SAS that is running on the same
Windows machine. You only need the following configuration definition keys. (Do not
specify any of the others).

**There is one additional requirement.** The sspiauth.dll file--also included in 
your SAS installation--must be in your system PATH environment variable, your 
java.library.path, or in the home directory of your Java client. You can search 
for this file in your SAS deployment, though it is likely
in SASHome\\SASFoundation\\9.4\\core\\sasext.

If you add the file to the system PATH environment variable, only list the path to 
the directory--do not include the file itself. For example:

::

    C:\Program Files\SASHome\SASFoundation\9.4\core\sasext 


Starting in version 2.4.1, there is a autocfg.py batch script available in saspy that
you can use to generate the sascfg_personal.py file for a Windows Local connection.
This script can also be run interactively. You can tell it the path/name of the file
you want it to create, tell it where your SASHome install directory it (if not in the default location).
And where to find java.exe, if java isn't already in your path to be found.

The default takes no parameters and creates sascfg_personal.py in the saspy install directory
to be used immediately. That assumes SAS in installed in the default location and the java command can be found.

See the example notebook that shows the various ways to use this script in the saspy-examples
github site: https://github.com/sassoftware/saspy-examples/blob/master/SAS_contrib/autocfg.ipynb



java      - 
    (Required) The path to the Java executable to use. 
classpath - 
    (Required) The CLASSPATH to the IOM client JAR files and saspyiom.jar.
encoding  -
    NOTE: as of saspy V2.4.2, you no longer need to set the encoding. SASpy
    will determine the SAS session encoding and map that to the Python encoding for you.

    This is the Python encoding value that matches the SAS session encoding of 
    the IOM server to which you are connecting. The Python encoding values can be 
    found at `encodings-and-unicode <https://docs.python.org/3.5/
    library/codecs.html#encodings-and-unicode>`_.
    The three most common SAS encodings, UTF8, LATIN1, and WLATIN1 are the 
    default encodings for running SAS in Unicode, on Unix, and on Windows,
    respectively. Those map to Python encoding values: utf8, latin1, and 
    windows-1252, respectively. 
autoexec -
    This is a string of SAS code that will be submitted upon establishing a connection.
    You can use this to preassign libraries you always want available, or whatever you want.
    Don't confuse this with the autoexec option of SAS which specifies a sas program file to be run.
    That is different. This is a string of SAS code saspy will submit after the session is created,
    which would be after SAS already included any autoexec file if there was one.

javaparms -
    The javaparms option allows you to specify Java command line options. These aren't generally needed, but this
    does allows for a way to specify them if something was needed.

lrecl -
    An integer specifying the record length for transferring wide data sets from SAS to Data Frames.

display -
    This is a new key to support Zeppelin (saspy V2.4.4). The values can be either 'jupyter' or 'zeppelin'. The default
    when this is not specified is 'jupyter'. Jupyter uses IPython to render HTML, which is how saspy has 
    always worked. To support Zeppelin's display method, a different display interface had to be added to saspy.
    If you want to run saspy in Zeppelin, set this in your configuration definition: 'display' : 'zeppelin', 

.. code-block:: ipython3

    # Windows client class path
    # build out a local classpath variable to use below for Linux clients  CHANGE THE PATHS TO BE CORRECT FOR YOUR INSTALLATION
    cpW  =  "C:\\Program Files\\SASHome\\SASDeploymentManager\\9.4\\products\\deploywiz__94472__prt__xx__sp0__1\\deploywiz\\sas.svc.connection.jar"
    cpW += ";C:\\Program Files\\SASHome\\SASDeploymentManager\\9.4\\products\\deploywiz__94472__prt__xx__sp0__1\\deploywiz\\log4j.jar"
    cpW += ";C:\\Program Files\\SASHome\\SASDeploymentManager\\9.4\\products\\deploywiz__94472__prt__xx__sp0__1\\deploywiz\\sas.security.sspi.jar"
    cpW += ";C:\\Program Files\\SASHome\\SASDeploymentManager\\9.4\\products\\deploywiz__94472__prt__xx__sp0__1\\deploywiz\\sas.core.jar"
    cpW += ";C:\\ProgramData\\Anaconda3\\Lib\\site-packages\\saspy\\java\\saspyiom.jar"


    # Windows client and Local Windows IOM server
    winlocal    = {'java'      : 'java',
                   'encoding'  : 'windows-1252',
                   'classpath' : cpW
                  }

.. note:: Having the ``'java'`` key is the trigger to use the IOM access method.
.. note:: When using the IOM access method (``'java'`` key specified), the 
         absence of the ``'iomhost'`` key is the trigger to use a local Windows
         session instead of remote IOM (it is a different connection type).



IOM to MVS SAS
~~~~~~~~~~~~~~
Yes, you can even connect to a SAS server running on MVS (Mainframe SAS). 
There are a couple of requirements for this to work right. First, you need version 2.1.5 or higher of this module.
There were a couple tweaks I needed to make to the IOM access method and those are in 2.1.5.

Also, you need to use the HFS file system for the WORK (and/or USER) library and you also need to set the default file
system to HFS so temporary files used by this module use HFS instead of the native MVS file system. You can still access
the native file system in the code you run, but for internal use, this module needs to access the HFS file system.
To set the default file system (options filesystem=hfs;) you can either set it in the workspace severs config file,
or you can submit the options statement from your python code after making a connection: 


::

    sas = saspy.SASsession()
    ll  = sas.submit('options filesystem=hfs;')


The other thing is to set the encoding correctly for this to work. MVS is an EBCDIC system, not ASCII. For the most part,
this is all handled in IOM for you, but there is a small amount of transcoding required internally in this module. The 
default encoding on MVS is OPEN_ED-1047, although it can be set to any number of other EBCDIC encodings. The default Python
encodings do not include the 1047 code page. I did find a 'cp1047' code page in a separate pip installable module which
seems to match the OPEN_ED-1047 code page. 

At the time of this writing, the only transcoding I need to do in python for this to work can be accomplished using the
'cp500' encoding which is part of the default set, so you don't have to install other modules. It's possible this could
change in the future, but I don't have any expectations of that for now, so using 'cp500' is ok if you don't want to
install other non-standard python modules. 


IOM using COM
=============
New in 3.1.0, this user contributed access method uses Windows COM to connect to the SAS IOM provider. It is similar to the other IOM access method, 
but there is no Java dependency. Connections from Windows clients to local and remote SAS 9.4 hosts are supported.

SAS Enterprise Guide or SAS Integration Technologies Client (a free download from SAS support) is required to install the SAS COM library on your client system.

The COM access method requires a Python module that saspy, in general, does not; pypiwin32  If you do not have this already installed before trying to use the COM
access method, you will likely see an error similar to this when trying to establish a connection. Just install that modules to solve this. 

>>> sas=saspy.SASsession()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/opt/tom/github/saspy/saspy/sasbase.py", line 360, in __init__
    self._io = SASSessionCOM(sascfgname=self.sascfg.name, sb=self, **kwargs)
  File "/opt/tom/github/saspy/saspy/sasiocom.py", line 197, in __init__
    self.pid = self._startsas()
  File "/opt/tom/github/saspy/saspy/sasiocom.py", line 212, in _startsas
    factory = dynamic.Dispatch('SASObjectManager.ObjectFactoryMulti2')
NameError: name 'dynamic' is not defined
>>>


To connect to a remote SAS server, you must specify the IOM host name and port number. The Class Identifier is also required, but is a constant which will be
prpvided on your behalf (starting in V3.1.4). The Class Identifier is a 32-character GUID that indicates the type of SAS server to connect to; in this case Workspace Server.

::

    proc iomoperate;
        list types;
    run;

::

    SAS Workspace Server 
        Short type name  : Workspace 
        Class identifier : 440196d4-90f0-11d0-9f41-00a024bb830c  /* this is a constant that doesn't change */

To connect to a local SAS instance, do not specify the ``iomhost`` paramter. Local connections do not require a host, port, class_id. 
Any specified port or class_id parameters will be ignored. Likewise, and provided username or password values are ignored on local connections.

iomhost - 
    The resolvable host name, or IP address to the IOM object spawner. Only required for remote connections. Don't specify for local connections.
iomport - 
    The port that object spawner is listening on for workspace server connections (workspace server port - not object spawner port!). Only required for remote connections. Don't specify for local connections.
class_id -
    This value turns out to be a constant which hasn't changed in years and probably never will. So, you shouldn't need to specify this. The value of
    '440196d4-90f0-11d0-9f41-00a024bb830c' will be used by default. Though you can specify it explitly here to override the default; but you should never need to.
    The IOM workspace server class identfier. Use ``PROC IOMOPERATE`` to identify the correct value for your configuration.
provider -
    (Required) The SAS IOM Data Provider is an OLE DB data provider that supports access to SAS data sets that are managed by SAS Integrated Object Model (IOM) servers. The 'sas.iomprovider' provider is recommended.
authkey -
    The keyword that starts a line in the authinfo file containing user and or password for this connection. See the IOM using Java above for more info.
omruser - 
    (**Discouraged**) The user ID is required but if this field is left blank,
    the user is **prompted** for a user ID at runtime, unless it's found in the authinfo file.
omrpw  - 
    (**Strongly discouraged**) A password is required but if this field is left
    blank, the user is **prompted** for a password at runtime, unless it's found in the authinfo file.
encoding  -
    NOTE: as of saspy V2.4.2, you no longer need to set the encoding. SASpy
    will determine the SAS session encoding and map that to the Python encoding for you.

    This is the Python encoding value that matches the SAS session encoding of 
    the IOM server to which you are connecting. The Python encoding values can be 
    found at `encodings-and-unicode <https://docs.python.org/3.5/
    library/codecs.html#encodings-and-unicode>`_.
    The three most common SAS encodings, UTF8, LATIN1, and WLATIN1 are the 
    default encodings for running SAS in Unicode, on Unix, and on Windows,
    respectively. Those map to Python encoding values: utf8, latin1, and 
    windows-1252, respectively.

.. code-block:: ipython3

    iomcom = {'iomhost': 'mynode.mycompany.org',
        'iomport': 8591,
        'class_id': '440196d4-90f0-11d0-9f41-00a024bb830c',
        'provider': 'sas.iomprovider',
        'encoding': 'windows-1252'}

.. note:: Having the ``'provider'`` key is the trigger to use the COM (IOM using COM) access method.
.. note:: When using the COM access method (``'provider'`` key specified), the 
         absence of the ``'iomhost'`` key is the trigger to use a local Windows
         session instead of remote IOM (it is a different connection type).



HTTP
=====
This is the access method for Viya. It does not connect to SAS 9.4. This access method accesses the Compute (micro) Service
of a SAS Viya deployment. The Compute Service launches Compute Servers, which are MVA SAS sessions found in the SPRE deployment
of the Viya installation. This is the equivalent of an IOM Workspace server, but in a Viya deployment.
So, it is still connecting to MVA SAS and all of the methods behave the same as they would with any other saspy access method.


The keys for this configuration definition dictionary are:

ip - 
    (Required) The resolvable host name, or IP address to the Viya Compute Service
port - 
    (Optional) The port to use to connect to the Compute Service. This will default to either 80 or 443 based upon the ssl key.
ssl - 
    (Optional) Boolean identifying whether to use HTTPS (ssl=True) or just HTTP. The default is True and will default to port 443 if
    the port is not specified. If set to False, it will default to port 80, if the port is not specified.
    Note that depending upon the version of python, certificate verification may or may not be required, later version are more strict.
    See the python doc for your version if this is a concern.
verify -
    (Optional) Also note that if Viya uses the default self signed ssl certificates it ships with, you will not be able to verify them,
    but that can be fine, and you can still use an ssl connection. You can use set 'verify' : False, in your config to
    turn off verification for this case. 
authkey -
    (Optional) The keyword that starts a line in the authinfo file containing user and or password for this connection. See the IOM using Java above for more info.
user - 
    (**Discouraged**)  The user ID is required but if this field is left blank,
    the user is **prompted** for a user ID at runtime, unless it's found in the authinfo file.
pw  - 
    (**Strongly discouraged**) A password is required but if this field is left
    blank, the user is **prompted** for a password at runtime, unless it's found in the authinfo file.

context -
    (Optional) The Compute Service has different Contexts that you can connect to. Think Appserver in IOM.
    if you don't provide one here, saspy will query the Service upon connecting and get a list of available Contexts and
    prompt you for which one to use.

options -
    (Optional) SAS options to include when connecting. These **must** be a Python list.

encoding -
    (Optional)
    NOTE: you no longer need to set the encoding. SASpy
    will determine the SAS session encoding and map that to the Python encoding for you.

    This is the Python encoding value that matches the SAS session encoding
    of the SAS session to which you are connecting. The Python encoding 
    values can be found at `encodings-and-unicode <https://docs.python.org/
    3.5/library/codecs.html#encodings-and-unicode>`_.
    The three most common SAS encodings, UTF8, LATIN1, and WLATIN1 are the 
    default encodings for running SAS in Unicode, on Unix, and on Windows,
    respectively. Those map to Python encoding values: utf8, latin1, and
    windows-1252, respectively. 

autoexec -
    (Optional) This is a string of SAS code that will be submitted upon establishing a connection.
    You can use this to preassign libraries you always want available, or whatever you want.
    Don't confuse this with the autoexec option of SAS which specifies a sas program file to be run.
    That is different. This is a string of SAS code saspy will submit after the session is created,
    which would be after SAS already included any autoexec file if there was one.

lrecl -
    (Optional) An integer specifying the record length for transferring wide data sets from SAS to Data Frames.

display -
    (Optional) This is a new key to support Zeppelin (saspy V2.4.4). The values can be either 'jupyter' or 'zeppelin'. The default
    when this is not specified is 'jupyter'. Jupyter uses IPython to render HTML, which is how saspy has 
    always worked. To support Zeppelin's display method, a different display interface had to be added to saspy.
    If you want to run saspy in Zeppelin, set this in your configuration definition: 'display' : 'zeppelin', 

.. code-block:: ipython3

    httpsviya = {'ip'      : 'sastpw.rndk8s.openstack.sas.com',
                 'context' : 'Data Mining compute context'
                 'authkey' : 'viya_user-pw',
                 'options' : ["fullstimer", "memsize=1G"]
                 }

.. note:: Having the ``'ip'`` key is the trigger to use the HTTP access method.


