# Networkteam.Neos.SelectiveMaintenance

Enable maintenance mode for specific documents in your page tree.

## Installation

Add this package to `composer.json` of your **site package**. 

```
cd Packages/Sites/Your.Site
composer require networkteam/neos-selectivemaintenance --no-update
```

Run node migration for adding default values.

```
flow node:migrate 20230216214800 --confirmation true
```

## Configuration

This package provides a processor for which replaces the common content with maintenance content. First of all you 
have to decide, at which position of your sites layout you'd like to render the maintenance content.
Some examples are:

- Replacing complete body
- Replacing main content only, for still showing navigation and footer  

Take a look into the [Neos demo package](https://github.com/neos/Neos.Demo). It provides several page types. Some of
them are:

- Page
- Landingpage
- Chapter
- Base

### Replacing complete body

If you would like to replace the complete body for a page being in maintenance, add the processor to base document 
(`Neos.Demo:Document.Base`)

*Packages/Sites/Neos.Demo/Resources/Private/Fusion/Document/Base.fusion*

```
/**
 * Prototype to render a page with all scripts and styles but with
 * a replaceable body and bodyClass via props
 */
prototype(Neos.Demo:Document.Base) < prototype(Neos.Fusion:Component) {
    body = null
    bodyClass = null

    renderer = Neos.Neos:Page {
        head {
            stylesheets {
                site = Neos.Demo:Document.Fragment.StyleSheets
            }
            javascripts {
                site = Neos.Demo:Document.Fragment.JavaScripts
            }
            metadata = Neos.Demo:Document.Fragment.MetaData
        }

        bodyTag.attributes.class = ${props.bodyClass}
        body = ${props.body}
+       body.@process.maintenaceContent = Networkteam.Neos.SelectiveMaintenance:MaintenanceContent 
    }
}
```

### Replacing main content only

For replacing main content only, you'll have to adjust each document separately.

Add the processor to the `mainContent` property. The example below shows the default page. Some procedure applies to 
the other documents.

*Packages/Sites/Neos.Demo/Resources/Private/Fusion/Document/Page.fusion*

```
/**
 * Default page-rendering for the Neos.Demo website
 */
prototype(Neos.Demo:Document.Page) < prototype(Neos.Fusion:Component) {
    mainMenu = Neos.Demo:Document.Fragment.Menu.Main
    secondaryMenu = Neos.Demo:Document.Fragment.Menu.Secondary
    metaMenu = Neos.Demo:Document.Fragment.Menu.Meta
    breadcrumbMenu = Neos.Demo:Document.Fragment.Menu.Breadcrumb
    languageMenu = Neos.Demo:Document.Fragment.Menu.Language

    mainContent = Neos.Demo:Document.Fragment.Content.Main
+   mainContent.@process.maintenanceContent = Networkteam.Neos.SelectiveMaintenance:MaintenanceContent
    footerContent = Neos.Demo:Document.Fragment.Content.Footer

    bodyClass = ${q(node).parents().count() >= 1 && q(node).children('[instanceof Neos.Neos:Document]').filter('[_hiddenInIndex=false]').count() > 0 ? 'has-subpages' : null}

    renderer = Neos.Demo:Document.Base {
        bodyClass = ${props.bodyClass}
        body = afx`
            <div class="top-navigation-wrap navbar-fixed-top">
                <div class="container">
                    {props.languageMenu}
                    {props.mainMenu}
                    {props.secondaryMenu}
                </div>
            </div>

            <div class="container">
                {props.breadcrumbMenu}
                {props.mainContent}
            </div>

            <footer role="navigation" class="navbar navbar-default">
                <div class="panel panel-default">
                    <div class="panel-body clearfix">
                        <div class="container">
                            {props.metaMenu}
                        </div>
                    </div>
                    <div class="panel-footer">
                        <div class="container">
                            {props.footerContent}
                        </div>
                    </div>
                </div>
            </footer>
        `
    }
}

```

## Usage

1. Create a page with maintenance information in your page tree, somewhere.
2. Select the page which should be in maintenance mode.
3. Enable maintenance mode and set previously created page as maintenance page