---
title: Debian Snapshot repo
date: 2020-09-05 06:50:32
tags:
---
## How to use debian snapshot repo

refer to [Official snapshot website](https://snapshot.debian.org/)
<!--more-->
replace apt source list with

```bash
deb	[check-valid-until=no] http://snapshot.debian.org/archive/debian/20190701T031013Z/ buster main contrib non-free
deb-src	[check-valid-until=no] http://snapshot.debian.org/archive/debian/20190701T031013Z/ buster main contrib non-free

deb	[check-valid-until=no] http://snapshot.debian.org/archive/debian-security/20190701T113436Z/ buster/updates main contrib non-free
deb-src	[check-valid-until=no] http://snapshot.debian.org/archive/debian-security/20190701T113436Z/ buster/updates main contrib non-free

deb	[check-valid-until=no] http://snapshot.debian.org/archive/debian/20190701T031013Z/ buster-updates main contrib non-free
deb-src	[check-valid-until=no] http://snapshot.debian.org/archive/debian/20190701T031013Z/ buster-updates main contrib non-free
```

For the timestamp, find appropriate one in the `Archives` section from the snapshot website.

## Package priority

The apt preference could be use to determine the package priority:

[apt_preferences\(5\)](https://manpages.debian.org/buster/apt/apt_preferences.5.en.html)

Be careful, the default priority is 500 if `APT::Default-Release` is not specified, otherwise the priority for the package that in the specified `APT::Default-Release` is 990.
