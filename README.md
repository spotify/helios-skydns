SkyDNS Service Registrar for Helios
-----------------------------------
## Running Tests

If you're using the Helios vagrant image (as described below), you shouldn't need to set any environment variables to run the integration tests.

Otherwise,
* Set `ETCD_SERVER` to location of your etcd server, e.g. `http://127.0.0.1:4001`.
* Set `SKYDNS_SERVER` to the address of your SkyDNS server.
* If you're running SkyDNS on a port that isn't 53, set `SKYDNS_PORT` to that port.

To run the integration tests, run `maven verify`.

Regular unit tests shouldn't require anything to run.

## Using The Plugin
To run the helios agent or master with this registrar:
* Set the `--service-registrar-plugin` option to the location of the jar file.
* Set the `--service-registry` option to the location of the etcd server you want to talk to, e.g. `http://192.168.33.10:4001`.
* Set the `--domain` option to which domain you want things to register themselves in.

## Getting A Working SkyDNS Server

If you're using the Helios Vagrant image, you can run the integration tests here against that and they should just work.

If not, you'll need to do something akin to this, adjusting `$GOPATH` and the `192.168.33.10` address bits below:

    # install golang and the VC systems it needs to `get` things
    sudo apt-get -y --force-yes install git-core
    sudo apt-get -y --force-yes install mercurial
    sudo apt-get -y --force-yes install golang

    # create a place to put stuff
    export GOPATH=/vagrant/gopath
    mkdir $GOPATH

    # get, build and install SkyDNS
    git clone git@github.com:skynetservices/skydns.git
    cd skydns
    go get -d -v ./... && go build -v ./...
    sudo cp skydns /usr/bin

    cd ..
    # get, build and install etcd
    git clone https://github.com/coreos/etcd
    cd etcd
    ./build
    sudo cp bin/etcd /usr/bin

    cd ..
    # start etcd and skydns
    etcd & disown
    sudo skydns -addr 192.168.33.10:53 -machines="127.0.0.1:4001" -nameservers="8.8.8.8:53,8.8.4.4:53"

These above instructions should work on debian or ubuntu machines generally.  The `apt-get` lines should be the only thing to change on non-ubuntu machines.

Findbugs
--------

To run [findbugs](http://findbugs.sourceforge.net) on this codebase, do
`mvn clean compile site`. This will build helios and then run an analysis,
emitting reports in `target/site/findbugs.html`.

To silence an irrelevant warning, add a filter match along with a justification
in `findbugs-exclude.xml`.
