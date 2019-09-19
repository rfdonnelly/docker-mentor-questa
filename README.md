# Docker Images for Questa

This repository provides Docker images for Questa.

These images were created for running UVM testbenches via a continuous
integration service with a FLEXnet license server.

## Image Types

The following image types are provided:

* base - Provides just enough for running UVM batch simulations
* user - A Verilog testbench template
* gui - Experimental image with X11 support

## Usage

1. Build the base image
2. Push to registry
3. Copy the user Dockerfile template into user testbench
4. Setup continuous integration service to use user
   Dockerfile

## Notes on Packaging

### Questa

Questa is large but can be pruned.  Both the Questa GCC and the system GCC must be installed.  The system GCC is required for DPI compilation (e.g. UVM).  Questa GCC is required for Questa VIP.  It may be possible to use just one but I haven't dug any deeper.

Install
* System GCC
* Questa 64-bit
* Questa provided GCC 64-bit

Do not install
* Questa 32-bit
* Questa provided GCC 32-bit

Prune
* The Questa provided GCC installs several versions.  Remove all but the latest GCC from $QUESTA_ROOT
* AVM, OVM, and old versions of UVM may be pruned.  See [prune-install.sh](https://github.com/rfdonnelly/docker-questa/blob/68c9f73136aa959ef42273f03ccf30718bd1c491/2019.1/base/prune-install.sh).  But note, these must also be removed from modelsim.ini.  See [prune-install.sh:61](https://github.com/rfdonnelly/docker-questa/blob/68c9f73136aa959ef42273f03ccf30718bd1c491/2019.1/base/prune-install.sh#L61)

Additional savings may be had but using a smaller base image (e.g. [Alpine](https://hub.docker.com/_/alpine)).

### Questa VIP

The Questa VIP install includes all available Questa VIP.  When packaging, only the VIP used should be packaged.  The QVIP has common components and protocol specific components.  Package all common components but only include the protocol components needed.

Exclude
* 32-bit components
* Versions compiled with older GCCs

Common components:
* include/
* linux_x86_64/
* questa_mvc_core/include/
* questa_mvc_core/lib/
* questa_mvc_core/linux_x86_64_gcc-5.3.0/libquesta*
* questa_mvc_src/sv/mvc_*

Protocol components
* questa_mvc_core/linux_x86_64_gcc-5.3.0/lib<protocol>*
* questa_mvc_src/sv/s<protocol>
