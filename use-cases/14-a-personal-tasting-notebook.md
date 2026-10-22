# 14 — A personal tasting notebook that recommends

**For:** buyers, sommeliers and serious amateurs who taste a lot and want their own judgement back
when it matters.

## The story

Marco buys wine for a restaurant. At tastings he photographs labels and jots notes. Later he wants
to ask "what did I like that would go with lamb, under thirty?" and get answers from his own notes,
not someone else's ratings.

## How the platform does it

- Each bottle is an artifact: the label photo, his notes, price, where he tasted it.
- A describer reads the label into fields; he corrects what it got wrong.
- Recall answers his questions from his own notebook; recommendations are ranked by what he liked
  before.

## What it uses today

Workspaces and collections, file upload (dropped on a workspace, or chosen with its Upload button,
which on a phone offers the camera), describers, recall, and the platform's "Answer from my notes"
operator, which cites the notes it answers from.

## To build

- Label reading from a photo.
- Ranking by what he liked before, and narrowing by price ("under thirty").

## Checked by

`usecase_14_a_personal_tasting_notebook.py`: Marco uploads a label photo from the app's Upload
button, and it is stored as taken; he asks "which wines go with lamb?" through the platform's
"Answer from my notes" operator, and the answer cites his two lamb notes in his own words and never
a colleague's, though theirs says lamb too. Reading the label and ranking by what he liked and paid
are not checked: they are not built.
