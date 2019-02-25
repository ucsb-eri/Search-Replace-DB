# My motivation for this fork
I forked this repo in an attempt to get the script working with PHP-7 (Fedora-28)

Another fork of this repo to pay attention to is: https://github.com/chrisbarnett1/Search-Replace-DB/tree/php72-support

## Discovered that the script was silently bailing on 'cache_views' table in drupal
The initial issue was an include() call that should be include_once() in the autoloader.
This caused a fatal error when it tried to load in the file again during a recursive unserialization (pretty sure that is the reason anyway).  I added lots of print statements trying to figure out where the issues was.  Those are now commented out.  So the unserialize() function was prefixed with an '@' to squelch unserialize errors, but end up squelching Fatal errors as well.

Modified srdb.cli.php to do an ini_set() on error reporting.  This squelches NOTICE errors, but allows fatal ones to make it through.

Hard coded in an exclusion for any table named cache_views.  In drupal this table would never finish processing because it would consume all the memory given to it. I ran it up to 2Gb of memory before just writing it off.

There are still a lot of incomplete class errors produced, but the ini_set above deals with that short term.  Hopefully somebody with better php autoloader skills can resolve that issue.

## Todo
* Want to clean up the code a bit, get rid of my extra print statements
* Should make sure some of the cli code is also updated in index.php
* Replace my hardcoded table exclusion with a cli argument to exclude specific tables
  * In particular, the cache_views table in drupal.
