PW-language-localized-URL
=========================

Version 0.0.6

ProcessWire's module to generate and parse localized URL for multilingual websites.

This module is useful to generate localized url using the language code as first 'folder', followed by localized titles of the nested pages:
`[language code]/[localized parent title]/[localized page title]`

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

So if you hit the URL: `/es/sobre/fondo/` it would know to display `/about/background/` using Spanish language

## How to  

### basic settings

This module is meant to be combined with use of FieldtypePageTitleLanguage, FieldtypeTextLanguage and FieldtypeTextareaLanguage.

To make it to work it's necessary to follow these steps:

1. define a template with this code:

	`<?php 
		$page = $modules->get('LanguageLocalizedURL')->parseUrl();
		echo $page->render();
	?>`

1. Enable "Allow URL Segments" for this template.
1. Create below the root page, one page per each language (en, it, fr, ...) (for the default language too if you don't want disable it in the settings) using that template.  
The names of these pages should match the names you set in the Languages settings of ProcessWire. These names will be used as first folder inside the URLs.  
On these pages could be useful to set the Status to Hidden.  
1. Enter in the module settings to inidicate the default language code (eg. 'en'), that will be mapped to the default languge inside ProcessWire.  
1. Create the pages of the website directly below the root page.  

### performance issue

To parse a localized URL the module needs to loop trough all the children pages of root (or of a parent page) searching for a localized title.  
This could be expensive if there are many pages/contents, so it's possible to generate urls using the page id as prefix:  

`[language code]/[localized parent title]/[page id]_[localized page title]`  
or  
`[language code]/[parent id]_[localized parent title]/[localized page title]`  
or  
`[language code]/[parent id]_[localized parent title]/[page id]_[localized page title]`  

Often the parents folders are not so many and could be used without any id.  
Anyway the desired behaviour is defined in the module settings.  

### enabling specific pages for specific languages

By default the module will map a page in any available language. Then the translated data will be selected. If missing, the default language will be used.  
If you don't want this behavior, and prefer to hide a page for a specific language follow these steps:

1. Create a Field of type Page and assign it a name like `language_published`.
1. In the `Input` tab of the field settings select the template created to map the languages.
1. Always in the `Input` tab select Checkboxes as `Input field type` and set 10 to `Columns of Checkboxes`.
1. Assing this new field the the templates of pages you want to lock.
1. Edit the page to manually enable the allowed language.

If you print a list of pages you will need to filter out the locked pages for a specific language:  

	$homepage = $pages->get("/"); 
	$lang = $user->language;

	$langname = $lang->name == 'default' ? 'en' : $lang->name;
	$langpage = $pages->get("/$langname/");

	// only include pages really published on the current language
	$children = $homepage->children("language_published=$langpage");
	$children->prepend($homepage); 

	foreach($children as $child) {
		$class = $child === $page->rootParent ? " class='on'" : '';
		echo "<li><a$class href='{$child->url()}'>{$child->title}</a></li>";
	}
	
### urlSegments

This module uses the url segments of ProcessWire to map the localized pages to real ones but the array of segment is re-indexed to contains only real segments.
This means that you can access `$input->urlSegments` as usual.  

Plus, the limit of 4 segments includes the page tree, so it could be necessary to change this limit in the `config.php` file:  

	$config->maxUrlSegments = 4;


