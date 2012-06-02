PW-language-localized-URL
=========================

Version 1.0

ProcessWire's module to generate and parse localized URL for multilingual websites.

This is meant to be combined with use of FieldtypePageTitleLanguage, FieldtypeTextLanguage and FieldtypeTextareaLanguage.

This module is useful to generate localized url using as first 'folder' the language code, and then the localized titles of the nested pages:
[language code]/[localized parent title]/[localized page title]

/about/
    /about/background/
    /about/history/
/contact/
/products/
    /product1/
    /product2/
/en/   <- language: English
/es/   <- language: Spanish
/fr/   <- language: French

So if you hit the URL: /es/sobre/fondo/ it would know to display /about/background/ using spanish language

The using of the title requires a loop trough all the children pages of root (or of a parent page) searching for a localized title.
This could be expensive if there are many pages/contents, so it's possible to generate url using the page id as prefix:
[language code]/[localized parent title]/[page id]_[localized page title]
or
[language code]/[parent id]_[localized parent title]/[localized page title]
or
[language code]/[parent id]_[localized parent title]/[page id]_[localized page title]

Often the parents folders are not so many and could be used without any id.
To generate url for a specific page use the method mlUrl (Multi language URL):
$page->mlUrl();
To include the id of the page:
$page->mlUrl(true);
To include the id of the parent too:
$page->mlUrl(true, true);

To parse the URL and open the correct page create an empty template file that contains this code:
<?php 
	$page = $modules->get('LanguageLocalizedURL')->parseUrl();
	include("./{$page->template}.php");
?>
Then create below the root page, one page per each language (en, it, fr, ...) (for the default language too) using that template.
These page names should match the names you set in the Languages settings of ProcessWire, and will be used as first folder inside the URLs.
Setup the module to inidicate the default language code, that will be mapped to the default languge inside ProcessWire.
After these steps you can create all the pages of the website directly below the root page.