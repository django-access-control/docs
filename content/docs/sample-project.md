---
title: "The sample project"
date: 2021-07-08
weight: 3
description: >
  This is a sample project demonstrating what Django Access Control is capable of.
---

Here we have a simple forum site for questions about ethics. Beside the built in models like `User` it has only one model: the `Question` (and -- unfortunately -- no model for answers...).

There are three types of users with different sets of permissions:

* Anonymous users can view (but not edit) questions, which have `is_published` set to `True`.
* Logged in users can see published questions and also ask new ones. Later on they can edit the body of the question, but not its title. The author of the question can see it even if it is not published.
* Staff members have the additional privileges of also seeing unpublished questions and changing the question's `is_published` value. It is `True` by default.

## Installation and setup

The source code can br found [here](https://github.com/django-access-control/example-project).

Assuming that you have a Unix-like shell, the setup is as follows:

```sh
git clone git@github.com:django-access-control/example-project.git
cd example-project
python3.9 -m venv venv
. ./venv/bin/activate
pip install -r requirements.txt
mv db.sqlite3.example db.sqlite3
./manage.py runserver
```

## An quick walk through

### The anonymous user's point of view

1. Open `http://127.0.0.1:8000/` in your browser. You should see the familiar Django admin interface.

![The index view](/img/anonymous-user-index-view.png)

2. Click on the blue text "Questions" and you see the list view of all published questions.

![The list view](/img/anonymous-user-list-view.png)

3. Click on the first question and you will see a read-only detailed view.

![The readonly view](/img/anonymous-user-question-view.png)

### The logged in user's point of view

1. Click "LOG IN" in the upper right corner and enter the username `Thomas_Aquinas` and password: `adminadmin`. You will be directed back to the index page. Now the "+ Add" button has appeared for "Questions".

![The index view](/img/aquinas-index-view.png)


2. Click again on the blue text "Questions" and you will see the list view. That time, however, one more question has appeared: "What is virtue?" is not published, but since Thomas Aquinas (the logged in user) is the author of that question, they can still see it.

![The list view](/img/aquinas-list-view.png)

3. Clicking on the title of that question will open the change view where you can make adjustments to the body of the question.

![The readonly view](/img/my-question-edit-view.png)

4. Opening a question by some other author (e.g. "What makes somebody's life meaningful?" will open a read-only view).

![The readonly view](/img/other-author-question-view.png)

### The staff member's point of view

Click "LOG OUT" in the upper right corner, click "LOG IN" and enter the username `Nāgārjuna` and password: `adminadmin`. Click on the blue text "Questions" and then click on any of the questions in the list view. Now you see a checkbox to alter the "Is published" value. 

![The list view](/img/change-is-published.png)

## And what is the significance of this?

The above screenshots do not look like something very special, do they? Any descent application should be able to do that, shouldn't it?

The value lies in the way access control is implemented: not in the template layer among your HTML code, but in the data access layer, so that all the views, REST & GraphQL controllers, CLI commands and other parts of the app can all go through the very same security layer. All the security configuration lives in the [`QuestionQuerySet`](https://github.com/django-access-control/example-project/blob/master/questions/models.py) class and -- thanks to sensible default -- is no more than 34 lines long.
