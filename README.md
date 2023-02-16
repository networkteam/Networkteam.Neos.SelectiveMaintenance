# Networkteam.Neos.SelectiveMaintenance

Enable maintenance mode for specific documents in your page tree.

## Installation

Add package via composer:

```
composer require networkteam/neos-selectivemaintenance
```

Run node migration for adding default values.

```
flow node:migrate 20230216214800 --confirmation true
```

## Usage

1. Create a page with maintenance information in your page tree, somewhere.
2. Select the page which should be in maintenance mode.
3. Enable maintenance mode and set previously created page as maintenance page