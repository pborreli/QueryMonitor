# Query Monitor

Query Monitor is a debugging plugin for anyone developing with WordPress. It has some unique features not yet seen in other debugging plugins, including automatic AJAX debugging and the ability to narrow down things by plugin or theme.

Query Monitor adds a toolbar menu showing an overview of the current page. Complete data is shown in the footer once you select a menu item.

Here's an example of Query Monitor's output. This is the panel showing aggregate database queries grouped by component, allowing you to see which plugins are spending the most time on database queries.

![Aggregate Database Queries by Component](https://raw.github.com/johnbillion/QueryMonitor/master/screenshots/db_query_components.png)

More screenshots at the bottom of the page.

## Database Queries

 * Shows all database queries performed on the current page
 * Shows **affected rows** and time for all queries
 * Show notifications for **slow queries** and **queries with errors**
 * Filter queries by **query type** (`SELECT`, `UPDATE`, `DELETE`, etc)
 * Filter queries by **component** (WordPress core, Plugin X, Plugin Y, theme)
 * Filter queries by **calling function**
 * View **aggregate query information** grouped by component, calling function, and type
 * Super advanced: Supports **multiple instances of wpdb** on one page

Filtering queries by component or calling function makes it easy to see which plugins, themes, or functions on your site are making the most (or the slowest) database queries. Query Monitor can easily tell you if your "premium" theme is doing a premium number of database queries.

## Hooks

 * Shows all hooks fired on the current page, along with hooked actions and their priorities
 * Filter hooks by **part of their name**
 * Filter actions by **component** (WordPress core, Plugin X, Plugin Y, theme)

## Theme

 * Shows the **template filename** for the current page
 * Shows the available **body classes** for the current page
 * Shows the active theme name

## PHP Errors

 * PHP errors (warnings, notices and stricts) are presented nicely along with their component and call stack
 * Shows an easily visible warning in the admin toolbar
 * Plays nicely with Xdebug

## HTTP Requests

 * Shows all HTTP requests performed on the current page (as long as they use WordPress' HTTP API)
 * Shows the response code, call stack, transport, timeout, and time taken
 * Highlights **erroneous responses**, such as failed requests and anything without a `200` response code

## Redirects

 * Whenever a redirect occurs, Query Monitor adds an `X-QM-Redirect` HTTP header containing the call stack, so you can use your favourite HTTP inspector to easily trace where a redirect has come from

## AJAX

The response from any jQuery AJAX request on the page will contain various debugging information in its header that gets output to the developer console. **No hooking required**.

AJAX debugging is in its early stages. Currently it only includes PHP errors (warnings, notices and stricts), but this will be built upon in future versions.

## Admin Screen

Hands up who can remember the correct names for the filters and hooks for custom admin screen columns?

 * Shows the correct names for **custom column hooks and filters** on all admin screens that have a listing table
 * Shows the state of `get_current_screen()` and a few variables

## Environment Information

 * Shows **various PHP information** such as memory limit and error reporting levels
 * Highlights the fact when any of these are overridden at runtime
 * Shows **various MySQL information**, including caching and performance related configuration
 * Highlights the fact when any performance related configurations are not optimal
 * Shows various details about **WordPress** and the **web server**
 * Shows version numbers for everything

## Everything Else

 * Shows the names and values for **query vars** on the current page, and highlights **custom query vars**
 * Shows any **transients that were set**, along with their timeout, component, and call stack
 * Shows all **WordPress conditionals** on the current page, highlighted nicely
 * Shows an overview at the top, including page generation time and memory limit as absolute values and as % of their respective limits

## A Note on Profiling

Query Monitor does not currently contain a profiling mechanism. The main reason for this is that profiling is best done at a lower level using tools such as [XHProf](https://github.com/facebook/xhprof).

However, it is likely that I will add some form of profiling functionality at some point. It'll probably be similar to how Joe Hoyle's [TimeStack](https://github.com/joehoyle/Time-Stack) does it, because that works nicely. Suggestions welcome.

## A Note on Query Monitor's Implementation

In order to do a few clever things, Query Monitor loads earlier than you ever thought humanly possible (almost). It does this by symlinking a custom `db.php` in your `WP_CONTENT_DIR`. This file (when present) gets included before the database driver is loaded, meaning this portion of Query Monitor loads before WordPress even engages its brain.

In this file is Query Monitor's extension to the `wpdb` class which:

 * Allows us to log **all** database queries (including ones that happen before plugins are loaded)
 * Logs the full stack trace for each query, which allows us to determine the component that's responsible for the query
 * Logs the query result, which allows us to display the affected rows or error message if applicable
 * Logs various PHP configurations before anything has loaded, which allows us to display a message if these get altered at runtime by a plugin or theme

If your `WP_CONTENT_DIR` isn't writable and therefore the symlink for `db.php` can't be put in place, Query Monitor still functions, but this extended functionality won't be available. You can manually create the symlink to the plugin's `wp-content/db.php` if you have permission. ([Related GitHub issue](https://github.com/johnbillion/QueryMonitor/issues/5))

# Screenshots

### Admin Menu

![Admin Menu](https://raw.github.com/johnbillion/QueryMonitor/master/screenshots/admin_menu.png)

### Database Queries

Database listing panel showing all queries, and the controls for filtering by query type, caller, and component

![Database Queries](https://raw.github.com/johnbillion/QueryMonitor/master/screenshots/db_queries.png)

A slow database query (over 0.1s by default) that has been highlighted in a separate panel

![Slow Database Queries](https://raw.github.com/johnbillion/QueryMonitor/master/screenshots/db_queries_slow.png)

### Aggregate Database Queries by Component

Ordered by most time spent

![Aggregate Database Queries by Component](https://raw.github.com/johnbillion/QueryMonitor/master/screenshots/db_query_components.png)

### Aggregate Database Queries by Calling Function

Ordered by most time spent

![Aggregate Database Queries by Calling Function](https://raw.github.com/johnbillion/QueryMonitor/master/screenshots/db_query_callers.png)

### Hooks

Hook listing panel showing all hooks, and the controls for filtering by name and component

![Hooks](https://raw.github.com/johnbillion/QueryMonitor/master/screenshots/hooks.png)

### HTTP Requests

Showing an HTTP request with an error

![HTTP](https://raw.github.com/johnbillion/QueryMonitor/master/screenshots/http.png)
