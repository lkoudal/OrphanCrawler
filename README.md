SiteCrawler
===========

A simple PHP class to crawl through a site extracting hyperlinks.

Given a starting URL, this class will find and navigate through all hyperlinks it can find, adding them to a list of crawled pages and a multi-dimensional array of the links contained on each page it crawls.

Optionally you can also pass arrays containing directories that should be ignored and additional filetypes to be accepted.

Usage:
```php
<?php
include 'site-crawler.class.php';
$crawler = new SiteCrawler('www.autohotkey.com'[, array('wiki', 'forum')[, array('html', 'htm', 'php', 'aspx')]]);
$output = $crawler->output(['php'|'xml']);
?>
```

By default, choosing XML output will save an XML file to the directory from which the script is used, providing you with the filename (for a link, redirect, whatever). Using PHP format will return a multi-dimensional array of crawled page count, crawled page list and a list of links-per-page.