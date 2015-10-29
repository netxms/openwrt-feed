# OpenWrt feed for NetXMS

How to use it:

1. Clone OpenWrt tree from git://git.openwrt.org/openwrt.git (or use snapshot, etc.)
1. Add `src-git netxms https://github.com/netxms/openwrt-feed.git` to `feeds.conf`
1. Run `./scripts/feeds update netxms` (or `./scripts/feeds update` to update all feeds)
1. `make menuconfig`, then select required package in `Administration` -> `NetXMS`.
1. `make package/netxms/install` to build

Packages will be in `bin/platform-name/packages/netxms/`
