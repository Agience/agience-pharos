# 05 — Planning an event with many people and dates

**For:** whoever is organising a wedding, a conference or a reunion, with vendors, guests and
deadlines.

## The story

Sarah is organising her daughter's wedding: a dozen vendors, two hundred guests, a budget and a
timeline. She wants one place where every quote, reply and deadline is, with reminders before things
are due and RSVPs that arrive by themselves.

## How the platform does it

- The event is a workspace; vendors, guests and the budget are collections.
- RSVPs and vendor replies come in through a form or mail and are filed as artifacts.
- A **Timer** sends reminders before each deadline; what is overdue is an **Inbox** item.
- Helpers get grants on the parts they work on, and nothing else.

## What it uses today

Workspaces and collections, grants, invitations, recall; the Timer for reminders and the Inbox for
what is overdue.

## To build

- **Inbound adapters** — a public RSVP form and replies by mail.

## Checked by

`usecase_05_planning_an_event.py`: Sarah keeps the wedding as a workspace with Vendors, Guests and
Budget. A helper joins Vendors by an invitation link as an editor, files a florist's reply there,
reads the other quotes, and reaches neither the guests, the budget nor the wedding itself; Sarah
finds the reply by its words. A reminder before the hall deposit is due is a Timer, armed from its
card in the app, that runs the platform's "Raise an Inbox item" operator once; the reminder waits in
Sarah's Inbox, naming the quote it is about. RSVPs arriving by themselves wait on inbound adapters.
