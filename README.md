# XDMoD Open OnDemand Module

The XDMoD Open OnDemand Module is an optional module for
tracking usage of the Open OnDemand software in XDMoD.

For more information about Open OnDemand please visit
[the Open OnDemand website](https://openondemand.org/)

For more information, including information about additional Open XDMoD
capabilities provided as optional modules, please visit
[the Open XDMoD website](https://open.xdmod.org).

# External Dependencies

The OnDemand module can use a GeoLite2 database to display location
information based on the IP address from the webserver logs. The IP to
location mapping is performed at data ingest time. The code has been
tested with [MaxMind's GeoLite2 City database](https://dev.maxmind.com/geoip/geoip2/geolite2/).

The database file is not distributed with Open XDMoD and must be
obtained seperately. If no database is present then all location
information will be marked as 'Unknown'. The database is not
required for the Open XDMoD module to display and process OnDemand
server log data.

# Install and configuration

The OnDemand module should be added to an existing working instance of
Open XDMoD version 9.5 or later. See the [Open XDMoD site](https://open.xdmod.org/9.5/)
for setup and install instructions.

## Source code install

The source package is installed as follows:

    $ tar zxvf xdmod-ondemand-9.5.0.tar.gz
    $ cd xdmod-ondemand-9.5.0
    # ./install --prefix=/opt/xdmod

Change the prefix as desired. The default installation prefix is /usr/local/xdmod. These instructions assume you are installing Open XDMoD in /opt/xdmod.

## RPM install

    # yum install xdmod-ondemand-9.5.0-1.0.el7.noarch.rpm

## Configuration

### Interactive script configuration

The Open OnDemand XDMoD module adds an additional main menu item to the XDMoD interactive setup software. Run the script as follows:

    # xdmod-setup

and select the ‘Open OnDemand’ option in the main menu. The Open OnDemand
section only has a single option: setup the database.  This option creates the
necessary database schema in the XDMoD
datawarehouse. You will need to provide the credentials for your MySQL root
user, or another user that has privileges to create databases. A single
database `modw_ondemand` will be created.  The database user that is
specified in the `portal_settings.ini` will be granted access to this
database.

### Manual configuration

If the database server is located on a different host than the webserver then it is necessary
to setup the database manually.

Create a database schema called `modw_ondemand` and grant permission for the XDMoD database user
acccount to access this schema.

Once the schema is created then the `acl-config` command should be run:

    $ /usr/xdmod/bin/acl-config

### Resource Setup

Add a new resource to Open XDMoD using the `xdmod-setup` script.
Instructions for adding the resource are on the [main Open XDMoD page](https://open.xdmod.org/9.5/configuration.html#resources)

The Open OnDemand resource must have a type set to "Gateway".

After the resource has been added then the `xdmod-ingestor` script must be run to load


  TODO: specify GeoIP data file location

TODO: specify webserver log file location


0) Add the `modw_ondemand` schema to the database.
1) Add a new resource to XDMoD using the XDMoD setup program. Resource type should be 'Gateway'.
2) run `xdmod-ingestor` to load the new resource into the database.

# Usage

Prerequisites:
1) Make sure you have added the OnDemand resource via `xdmod-setup` and run `xdmod-ingestor` to load the resource
   into XDMoD's datawarehouse.

To ingest the OnDemand weblogs. You need to specify the hostname of the ondemand instance exactly
as it appears in the server logs. This includes the `https://` parts and any port numbers but
do not include the trailing forward slash. You also need to specify the XDMoD resource name.
For example:

    /usr/share/xdmod/tools/etl/etl_overseer.php -p ondemand.log-ingestion -p ondemand.aggregation -d OOD_HOSTNAME=https://ondemand.ccr.buffalo.edu -d OOD_RESOURCE_CODE=ondemand -v debug
