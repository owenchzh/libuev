libuEv | Simple event loop for Linux
====================================
[![Travis Status][]][Travis] [![Coverity Status][]][Coverity Scan]


* [Introduction](#introduction)
* [API](API.md#overview)
  * [Create an Event Context](API.md#create-an-event-context)
  * [Register an Event Watcher](API.md#register-an-event-watcher)
  * [Start Event Loop](API.md#start-event-loop)
  * [Summary](API.md#summary)
* [Using -luev](API.md#using--luev)
* [Joystick Example](API.md#joystick-example)
* [Build & Install](#build--install)
* [Origin & References](#origin--references)


> **NOTE:** Incompatible failure mode changes in v2.0 compared to v1.x!

Introduction
------------

[libuEv][] is a simple event loop in the style of the more established
[libevent][1], [libev][2] and the venerable [Xt(3)][3] event loop.  The
*u* (micro) in the name refers to both the small feature set and the
small size overhead impact of the library.

Failure mode changes introduced in v2.0 may affect users of v1.x, See
the [ChangeLog][] for the full details.

The [API documentation](API.md) is available as a separate document.


Example
-------

Notice below how watcher `UEV_ERROR` conditions must be handled by each
callback.  I/O watchers must also check for `UEV_HUP`.  Both errors are
usually fatal, libuEv makes sure to stop each watcher before a callback
runs, leaving it up to the callback to take appropriate action.

```C
#include <stdio.h>
#include <uev/uev.h>

static void cb(uev_t *w, void *arg, int events)
{
    if (UEV_ERROR == events) {
        puts("Problem with timer, attempting to restart.");
        uev_timer_start(w);
    }
	puts("Every other second");
}

int main(void)
{
	uev_t timer;
	uev_ctx_t ctx;

	uev_init(&ctx);
	/* Set up a timer watcher to call cb() every other second */
	uev_timer_init(&ctx, &timer, cb, NULL, 2 * 1000, 2 * 1000);

	return uev_run(&ctx, 0);
}
```


Build & Install
---------------

libuEv use the GNU configure and build system.  To try out the bundled
examples, use the `--enable-examples` switch to the `configure` script.
There is also a limited unit test suite that can be useful to learn how
the library works.

```sh
./configure
make -j5
make test
sudo make install-strip
sudo ldconfig
```

The resulting .so file is ~14 kiB.


Origin & References
-------------------

[libuEv][] originates from [LibUEvent][8] by [Flemming Madsen][], but
with a rewritten and much cleaner API.  It is now more similar to the
famous [libev][2] by [Mark Lehmann][].  Another library inspiring this
project is [picoev][9] by [Oku Kazuho][].

[libuEv][] is developed and maintained by [Joachim Nilsson][].  It is
built for and developed on GNU/Linux systems, patches to support *BSD
and its [kqueue][] interface are most welcome.


[1]: http://libevent.org
[2]: http://software.schmorp.de/pkg/libev.html
[3]: http://unix.com/man-page/All/3x/XtDispatchEvent
[8]: http://code.google.com/p/libuevent/
[9]: https://github.com/kazuho/picoev
[ChangeLog]:       https://github.com/troglobit/libuev/blob/master/ChangeLog.md
[Travis]:          https://travis-ci.org/troglobit/libuev
[Travis Status]:   https://travis-ci.org/troglobit/libuev.png?branch=master
[Coverity Scan]:   https://scan.coverity.com/projects/3846
[Coverity Status]: https://scan.coverity.com/projects/3846/badge.svg
[LibuEv]:          https://github.com/troglobit/libuev
[kqueue]:          https://github.com/mheily/libkqueue
[Oku Kazuho]:      https://github.com/kazuho
[Mark Lehmann]:    http://software.schmorp.de
[Joachim Nilsson]: http://troglobit.com
[Flemming Madsen]: http://www.madsensoft.dk
[Dave Zarzycki, Apple]: http://www.youtube.com/watch?v=cD_s6Fjdri8
