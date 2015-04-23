---
title: Serendipity framework
layout: default
---

The test example logs in as an admin and creates a user with random credentials and settings. It logs in as that user and posts and validates random content. It stores user settings preferences (e.g. preference for wysiwyg) in the test DB. It validates that the application honours those settings.

# Install Serendipity #
This worked on Fedora 20. I've cloned the github repository up to commit e12b998bf7 into _/var/www/html/serendipity_ (git clone https://github.com/s9y/Serendipity.git). Install Serendipity as per its [documentation](http://www.s9y.org/36.html).

The easiest way is using Serendipity with SQLite. If you choose SQLite and you don't provide any db-related fields, the installer creates a .db file under webroot (e.g. _`serendipity_a2a42d7580267f6dcf1a3fd779040498.db`_) and puts that filename (excluding .db extension) in field dbName in _serendipity\_config\_local.inc.php_. The default _dbPrefix_ is 'serendipity_'._

# Install SeLite Serendipity test framework #
Apply [InstallFramework](InstallFramework). The framework and tests are in [serendipity](https://code.google.com/p/selite/source/browse/serendipity) folder.

## Scope ##
The test framework tries not to rely on
  * text constants,
  * CSS classes or
  * exact HTML structure.
Therefore it should work with embedded configurations or various languages. It could be used with custom templates (unless they differ a lot). It uses
  * 'name' attribute of inputs
  * URLs of links
  * some element IDs (e.g. serendipity\_iframe)

# Maintaining the framework #
  * Keep it honour configuration of Serendipity installation - as per http://www.s9y.org/66.html > Paths, Permalinks, General settings, Appearance and options.
  * If you need to target elements by text, see http://www.s9y.org/137.html > Internationalization

The framework honours Serendipity settings (global and user-specific - stored in serendipity\_config). @TODO: Make it update the test DB to reflect change of settings. Check whether the config pages can be customised via templates or not.

## Boolean fields ##
Serendipity uses string literals 'true', 'false' for some fields which have SQLite column type _boolean_ (e.g. _allow\_comments_ and _moderate\_comments_ in _serendipity\_entries_. Do not use Javascript boolean literals true or false, because they get transformed to SQLite integer 1 or 0, respectively.

## webRoot ##
The framework doesn't use SeLite Settings configuration field _extensions.selite-settings.common.webRoot_ (and it removes this field). It uses Serendipity field 'URL to blog' (stored in serendipity\_config with name='defaultBaseURL') as well as configuration for name='serendipityPath' and name='indexFile'.

## multihosting ##
The framework doesn't autodetect the paths based on HTTP host - it would be complicated, because it would also need to pick a test DB relevant to the host etc. If you'd like to test with autodetected multiple hosts, probably the easiest way is to have one test suite per host. Have the test suites in separate folders and associate each folder with a separate [Settings](SettingsOverview) configuration via [SettingsManifests](SettingsManifests). Those test suites can share same test cases. Or have same test suites symlinked from multiple directories (at the same depth), each folder having a separate configuration.