### README

This repo illustrates a bug in Rails/Sprockets whereby the digest of an asset
will change on each precompile if a lambda is given to `Rails.configuration.asset_host`

You can verify this currently by running:

`rake assets:precompile`

Note the output of the application.js

If you then run

`rm -r public/assets; rake assets:precompile`

Again, you'll see a different digest is generated.

Now, if you comment out [the line that exposes the bug](https://github.com/bradrobertson/assets-precompile-digest-bug/blob/master/config/application.rb#L23)
and precompile your assets, you should notice that application.js digest stays the same.

On my machine, I consistently get: `application-955c47b3c9321a709fe0a90a0d67bffe.js`

This is *definitely* a bug as it will cause invalid assets to potentially be served
depending on the machine that compiles them. For instance we have a utility
machine that sends out emails. The digested assets from that machine are different
from the ones on our app server (which ultimately serves them) so when an email goes
out, the asset urls generated don't actually exist on our web machine, thus we get
a missing attribute error.
