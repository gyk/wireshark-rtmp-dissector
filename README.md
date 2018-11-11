wireshark-rtmp-dissector
========================

A standalone RTMP dissector for Wireshark. It can be compiled out-of-tree and run as a plugin. The
dissector code is mostly copied from [Wireshark repository][rtmpt]. I just reorganized the directory
structure and made minor modifications to the code.

[rtmpt]: https://github.com/wireshark/wireshark/blob/wireshark-2.6.2/epan/dissectors/packet-rtmpt.c

Compilation
-----------

To compile the shared library, simply run the following:

    $ mkdir build && cd build
    $ cmake ..
    $ make

To install into `${HOME}/.config/wireshark/plugins`, run:

    $ make install

For debugging with GDB or LLDB, use `cmake -DCMAKE_BUILD_TYPE=Debug ..` instead.

You need to have Wireshark installed with the epan headers. Currently it is only tested for
Wireshark 2.6.2 on macOS.

References
----------

- <https://github.com/wireshark/wireshark/blob/master/doc/README.plugins>
- <https://github.com/ayyaruq/ffxiv-dissector>
