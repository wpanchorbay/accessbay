---
title: "Finding things: search and filters"
description: "How to use AccessBay's search dialog, the filter row on list screens, saved views, and shareable links."
---
AccessBay has two ways to find something. The search dialog looks across the whole plugin. The filter row narrows the
list you are looking at.

## The search dialog

Open it with the **Search** box in the sidebar, or press `/` on any AccessBay screen. The `/` key does nothing while
your cursor is in a text field, and it ignores the Ctrl, Cmd and Alt keys, so it never takes over a shortcut of your
browser.

Type at least two characters. AccessBay waits a moment after you stop typing, then shows the results in groups:
Licences, Customers, Orders, Products, and Settings and dashboard. Each group shows up to eight results, and Settings
and dashboard up to twelve. The status line
under the field says how many results there are, or "No results".

- Press the up and down arrow keys to move through the results.
- Press Enter to open the highlighted one. If none is highlighted, Enter opens the first.
- Press Escape to close the dialog.

### What it searches

| Group | You can search by |
|---|---|
| Licences | The licence's id, the last four characters of its key, or a whole key pasted in |
| Customers | The customer's id, or the start of an email address, last name or company |
| Orders | The order's id, the exact order number, or the start of the buyer's email address |
| Products | The product's id, or any part of its name or slug |
| Settings and dashboard | Words from the name or help text of a setting, a settings section or a dashboard panel |

A pasted licence key is searched for and never saved. The dialog sends it in the body of the request, never in the
page address, and a licence result shows only the last four characters of the key. Customers that were merged into
another customer don't show up.

### Settings in the results

Search also finds settings, so "retention" takes you to the retention fields without you knowing which section holds
them. It covers every settings section and each field in it, the API keys screen, the Email providers screen, the
Danger zone and the dashboard panels. It matches the label and the help text of a field. It never looks at the value
you saved.

A result shows where it lives, for example "Settings > Email settings". A field result opens its section, scrolls to
the field and puts the cursor in it. Every word you type has to match, so a longer search gives fewer results. Label
matches come before help-text matches, and there are at most twelve settings results.

Only people who may manage AccessBay's settings see settings results. Everyone else sees the dashboard results only.

## The filter row on list screens

Products, Orders, Licences, Activations and Customers share one layout: a single row above the table, then the table,
then the pager.

- **Search box.** Searches that list only. AccessBay waits a moment after you stop typing. Press Escape to empty the
  box. What it searches depends on the screen, and the placeholder text says so.
- **Filter buttons.** One button per filter. The button shows its name and your choice, for example "Status: Active,
  Draft", or "Status: All" when nothing is chosen. When the choice is long, the button says "3 selected" instead.
- **Clear filters.** Appears only when a search or a filter is set. It empties them all in one step.
- **Save view.** Appears when the list differs from its default and is not already a saved view. See below.
- **Views.** Appears once you have saved a view. See below.
- **Columns.** The columns button lists the columns with a checkbox each, so you can hide the ones you don't need.

A filter can take more than one value. Its menu is a list of checkboxes that stays open while you tick, so you can
choose "Active" and "Draft" in one visit. A row matches a filter when it has any of the ticked values. Different
filters combine: "Status: Active" and "Type: Software" shows only the rows that are both.

On the screens where a column can be sorted (Products, Licences, Activations and Customers), click its header to sort by it. Click again to reverse the order.

The pager under the table shows "Page 2 of 7", the total number of rows, and how many rows fit on a page (10, 25, 50
or 100). If nothing matches, the table says "No ... found. Try other filters or a different search." On a screen that
has never had any rows, it explains what creates them instead.

The Customers list has a search box, columns and sorting, but no filters, so it has no filter buttons and no **Clear
filters**. The email log tabs have their own filters ([Email setup](/guide/email/#filtering-the-logs)).

## Saved views

A saved view keeps a search, the filters, the sort order and the columns of one list under a name, so you can come back
to them in one click.

1. Set up the list the way you want it.
2. Select **Save view**.
3. Type a name and confirm. A name needs at least one character and at most 60. Two views of the same list can't
   share a name.

The **Views** menu then lists your saved views for that screen. Choose one to open it. While a view is open, the menu
has **Delete this view**. A view stores its filters, search, sort and columns but not the page you were on.

Saved views belong to your user account, so other people on the site don't see them. You can keep up to 50.

## Links you can share

AccessBay keeps the state of a list in the page address, after the `#`. For example:

```text
https://example.com/wp-admin/admin.php?page=accessbay#/orders?payment=paid&q=ada&cols=customer,total
```

Filters with several values join them with commas, as in `status=active,draft`. A value that is the default is left
out, and a value AccessBay doesn't know is ignored.

Because of this:

- Reloading the page brings back the same list.
- The browser's Back and Forward buttons step through the lists you looked at.
- You can copy the address and send it to a colleague. It opens in wp-admin, so they need to be logged in with access
  to AccessBay.

The search dialog is the exception. Whatever you type there never enters the address.
