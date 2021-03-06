OpenDHT
===
A lightweight C++11 Distributed Hash Table implementation originally based on https://github.com/jech/dht by Juliusz Chroboczek.

 * Light and fast C++11 Kademlia DHT library.
 * Distributed shared key->value data-store
 * Clean and powerfull distributed map API.
 * Storage of arbitrary binary values up to 64 kB. Keys are 160 bits long.
 * Optional public key cryptography layer providing data signature and encryption (using GnuTLS).
 * IPv4 and IPv6 support.

Documentation
-
See the wiki: <https://github.com/savoirfairelinux/opendht/wiki>

Examples
-
The `tools` directory includes two simple example programs :
* `dhtnode`, a command line tool, mostly used for debuging, allowing to perform all operations supported by the library (get, put etc.) with string values.
* `dhtchat`, a very simple IM client working over the dht.

Example program launching a DHT node, connecting to the network and performing some basic operations:
```c++
#include <opendht.h>
#include <vector>

int main()
{
    dht::DhtRunner node;

    // Launch a dht node on a new thread, using a
    // generated RSA key pair, and listen on port 4222.
    node.run(4222, dht::crypto::generateIdentity(), true);

    // Join the network through any running node,
    // here using a known bootstrap node.
    node.bootstrap("bootstrap.ring.cx", "4222");

    // put some data on the dht
    std::vector<uint8_t> some_data(5, 10);
    node.put("unique_key", some_data);

    // put some data on the dht, signed with our generated private key
    node.putSigned("unique_key_42", some_data);

    // get data from the dht
    node.get("other_unique_key", [](const std::vector<std::shared_ptr<dht::Value>>& values) {
        // Callback called when values are found
        for (const auto& value : values)
            std::cout << "Found value: " << *value << std::endl;
        return true; // return false to stop the search
    });

    // here we could wait for some operations to complete
    // instead of ending now.

    // wait for dht threads to end
    node.join();
    return 0;
}
```

How-to build and install
-
```bash
# clone the repo
git clone https://github.com/savoirfairelinux/opendht.git

# build and install
cd opendht
./autogen.sh && ./configure
make
sudo make install
```

How-to build a simple client app
-
```bash
g++ main.cpp -std=c++11 -lopendht -lgnutls
```

Licence
-
Copyright (C) 2014-2015 Savoir-Faire Linux Inc.

Licenced under the GNU General Public License version 3, though the core routing library (dht.cpp) is licenced under the MIT licence.

This project is independent from another project called OpenDHT (Sean Rhea. Ph.D. Thesis, 2005), now extinct.

Dependencies
-
- GnuTLS 3.1+, used to compute hashes and for the identity layer.
- Build tested with GCC 4.8+ (Linux, Android, Windows with MinGW), Clang/LLVM (Linux, OS X).

