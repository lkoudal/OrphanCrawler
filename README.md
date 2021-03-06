#OrphanCrawler#

A collection of PHP classes to crawl a website and its FTP server, then compare the two to find orphaned files.

Both SiteCrawler and FTPCrawler classes can be used individually without performing the final comparison, making it somewhat flexible. There are also options that allow you to specify which directories and filetypes to ignore. There is no cache system or any method to determine the staleness of a page, and the URL queue will be processed according to the order the URLs are found - there is no prioritisation in place.

##Classes##

###OrphanCrawler###

`orphan-crawler.class.php`

Can be used to operate one or both of the other classes in this project. Provides several ways of interacting with the other classes in order to make it more versatile and usable in many situations.

**Usage:**
```php
<?php
include 'orphan-crawler.class.php';

$crawler = new OrphanCrawler();
$crawler->ftp('www.example.com', 'fred', 'awesomepassword123');
$crawler->site('www.example.com');

$ftp_settings = array(
	'ftp'=>array(
		'passive'=>true,
		'ignore_types'=>array(
			'js',
			'css',
			'png'
		)
	)
);
$ftp_config = $crawler->settings($ftp_settings);

$site_settings = array(
	'site'=>array(
		'ignore_dirs'=>array(
			'cgi-bin',
			'_uploads'
		),
		'file_types'=>array(
			'aspx'
		)
	)
);
$site_config = $crawler->settings($site_settings);

$output = $crawler->output('compare', 'php');
echo "<pre>" . print_r($output, true) . "</pre>";

?>
```

**Output Formats**
- `php` is the default output which provides a multi-dimensional array of the orphaned files, FTP output $list and Site output $links.
- `xml` uses the arrays from the 'php' output of the other two classes to generate an XML file containing the list of orphaned files, settings used for FTP, list of files on the server and the list of pages crawled, along with the count of entries in each section.
- `html` is very similar to 'xml' however, the three classes generate their own HTML files along with an index file with links to the three. The default directory structure for this is `./OrphanCrawler_Report/<url>/<YYYY-MM-DD_hh-mm-ss>/`.

###FTPCrawler###

`ftp-crawler.class.php`

Given the host address, username and password of a site's FTP server, this class will navigate through all folders creating an array of all files found.

Optionally you can also pass an array of settings such as the directory to treat as root, directories to ignore and whether or not to use passive mode.

**Usage:**
```php
<?php
include 'ftp-crawler.class.php';

$crawler = new FTPCrawler('www.example.com', 'fred', 'awesomepassword123');

$settings = array(
	'passive'=>true,
	'ignore_types'=>array(
		'js',
		'css',
		'png'
	),
	'ignore_dirs'=>array(
		'cgi-bin',
		'_uploads'
	)
);
$results = $crawler->settings($settings);

$output = $crawler->output('php');
echo "<pre>" . print_r($output, true) . "</pre>";
?>
```

**Output Formats**
- 'php' is the default output which provides a multi-dimensional array of crawled page count, crawled page list and a list of links-per-page.
- 'xml' outputs a basic document containing the settings used for the connection, the total count of files found and the full list of files as both paths and URLs. As an XML file, obviously.

###SiteCrawler###

`site-crawler.class.php`

Given a starting URL, this class will find and navigate through all hyperlinks it can find, adding them to a list of crawled pages and a multi-dimensional array of the links contained on each page it crawls.

Optionally you can also pass arrays containing directories that should be ignored and additional filetypes to be accepted.

**Usage:**
```php
<?php
include 'site-crawler.class.php';

$crawler = new SiteCrawler('www.example.com', array(true, false));

$settings = array(
	'ignore_dirs'=>array(
		'cgi-bin',
		'_uploads'
	),
	'file_types'=>array(
		'aspx'
	)
);
$results = $crawler->settings($settings);

$output = $crawler->output('php');
echo "<pre>" . print_r($output, true) . "</pre>";

?>
```

**Output Formats**
- `php` is the default output which provides a multi-dimensional array of crawled page count, crawled page list and a list of links-per-page.
- `xml` output saves the list of links-per-page to an XML file. The file name is generated from the initial URL.
- `sitemap` outputs a very simple XML file according to the [Sitemaps.org](http://sitemaps.org/) [0.9 Schema](http://www.sitemaps.org/protocol.html) which means it is compatible with Google.

###RobotsCrawler###

`robots-crawler.class.php`

Given a URL, this class will attempt to load the website's `robots.txt` file to discover the crawling rules set by the server admin. This class is designed to be used as part of `SiteCrawler`, using it standalone is pretty much pointless except for testing the regex - as such, there are two examples below: one as part of `SiteCrawler`; one showing debugging.

Can take an optional parameter to set a custom user-agent. 

Uses `@file()` as a fallback if `CURL` is not installed.

**Usage:**

Example 1
```php
<?php
include 'site-crawler.class.php';

$crawler = new SiteCrawler('www.example.com', array(true, false));
$results = $crawler->settings();

echo "<pre>" . print_r($results['robot'], true) . "</pre>";

?>
```
Example 2
```php
<?php
include 'robots-crawler.class.php';

$robot = new RobotsCrawler('www.example.com', false);
$rules = $robot->settings();

echo "<pre>" . print_r($rules, true) . "</pre>";

?>
```

There are no options here. During normal use (as part of `SiteCrawler`) it will return either `true` or `false`, determining whether or not the current path should be crawled.

##To Do##

- `<form>` action pages need to be crawled (SiteCrawler)
- Make `SiteCrawler::relativePathFix()` more adaptable and competent
  - Process blog-style paths
- Allow for listing links to external websites/other protocols
  - Main problem with this is javascript links
- Look into other output formats
  - Expand the `sitemap` format in SiteCrawler
  - HTML output
    - Template system?
    - jQuery for removing items from list?
- Error logging
  - Log file?
  - Per-class array?
- `robots.txt` compliance
