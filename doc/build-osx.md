Mac OS X Build Instructions and Notes
====================================
This guide will show you how to build mazad(headless client) for OSX.
The commands in this guide should be executed in a Terminal application.
The built-in one is located in `/Applications/Utilities/Terminal.app`.

Preparation
-----------
Install the OS X command line tools:

`xcode-select --install`

When the popup appears, click `Install`.

Then install [Homebrew](https://brew.sh).

Dependencies
----------------------

    brew install automake berkeley-db4 libtool boost miniupnpc openssl pkg-config protobuf python3 qt libevent

See [dependencies.md](dependencies.md) for a complete overview.

If you want to build the disk image with `make deploy` (.dmg / optional), you need RSVG

    brew install librsvg

If you want to build with ZeroMQ support
    
    brew install zeromq

NOTE: Building with Qt4 is still supported, however, could result in a broken UI. Building with Qt5 is recommended.

Berkeley DB
-----------
It is recommended to use Berkeley DB 4.8. If you have to build it yourself,
you can use [the installation script included in contrib/](/contrib/install_db4.sh)
like so

```shell
./contrib/install_db4.sh .
```

from the root of the repository.

**Note**: You only need Berkeley DB if the wallet is enabled (see the section *Disable-Wallet mode* below).

Build Maza Core
------------------------

### Building `mazad`
1. Clone the maza source code and cd into `maza`

        git clone https://github.com/mazacoin/maza
        cd maza

        git clone https://github.com/maza/maza.git
        cd maza

2.  Build mazad:

    You can disable the GUI build by passing `--without-gui` to configure.

        ./autogen.sh
        ./configure
        make

3.  It is recommended to build and run the unit tests:

        make check

Creating a release build
------------------------
You can ignore this section if you are building `mazad` for your own use.

mazad/maza-cli binaries are not included in the Maza-Qt.app bundle.

If you are building `mazad` or `Maza-Qt` for others, your build machine should be set up
as follows for maximum compatibility:

        make install

    or

        cd ~/maza/src
        cp mazad /usr/local/bin/
        cp maza-cli /usr/local/bin/
Once dependencies are compiled, see release-process.md for how the Mazacoin-Qt.app
bundle is packaged and signed to create the .dmg disk image that is distributed.

Running
-------

It's now available at `./mazad`, provided that you are still in the `src`
directory. We have to first create the RPC configuration file, though.

Run `./mazad` to get the filename where it should be put, or just try these
commands:

    echo -e "rpcuser=mazarpc\nrpcpassword=$(xxd -l 16 -p /dev/urandom)" > "/Users/${USER}/Library/Application Support/Maza/maza.conf"
    chmod 600 "/Users/${USER}/Library/Application Support/Maza/maza.conf"

    chmod 600 "/Users/${USER}/Library/Application Support/Maza/maza.conf"

    tail -f $HOME/Library/Application\ Support/Maza/debug.log

You can monitor the download process by looking at the debug.log file:

    tail -f $HOME/Library/Application\ Support/Maza/debug.log

Other commands:
-------

    ./mazad -daemon # to start the maza daemon.
    ./maza-cli --help  # for a list of command-line options.
    ./maza-cli help    # When the daemon is running, to get a list of RPC commands

Using Qt Creator as IDE
------------------------
You can use Qt Creator as an IDE, for maza development.
Download and install the community edition of [Qt Creator](https://www.qt.io/download/).
Uncheck everything except Qt Creator during the installation process.

1. Make sure you installed everything through Homebrew mentioned above
2. Do a proper ./configure --enable-debug
3. In Qt Creator do "New Project" -> Import Project -> Import Existing Project
4. Enter "maza-qt" as project name, enter src/qt as location
5. Leave the file selection as it is
6. Confirm the "summary page"
7. In the "Projects" tab select "Manage Kits..."
8. Select the default "Desktop" kit and select "Clang (x86 64bit in /usr/bin)" as compiler
9. Select LLDB as debugger (you might need to set the path to your installation)
10. Start debugging with Qt Creator

Notes
-----

* Tested on OS X 10.8 through 10.13 on 64-bit Intel processors only.

* Building with downloaded Qt binaries is not officially supported. See the notes in [#7714](https://github.com/bitcoin/bitcoin/issues/7714)
