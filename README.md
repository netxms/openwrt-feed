# OpenWrt feed for NetXMS

How to use it:

1. Clone OpenWrt tree from https://git.openwrt.org/openwrt/openwrt.git (or use snapshot, etc.)
1. Copy default feeds.conf: `cp feeds.conf.default feeds.conf`
1. Add `src-git netxms https://github.com/netxms/openwrt-feed.git` to `feeds.conf`
1. Update and install default feeds: `./scripts/feeds update -a && ./scripts/feeds install -a`
1. Run `make menuconfig` and configure image (platform, desired libc, etc.)
1. `make menuconfig`, then:
   1. Configure image (platform, desired libc, etc.)
   1. Select ssl/non-ssl agent package in `Administration` -> `NetXMS`.
1. Build toolchain, then package itself:
   1. `make tools/install -j$(nproc)`
   1. `make toolchain/install -j$(nproc)`
   1. `make package/netxms/compile -j$(nproc)`

Alternatively you can build whole image with `make -j$(nproc)`.

Packages will be in `bin/platform-name/packages/netxms/`
