Project 4: Conference Organization App

App Engine application for the Udacity training course.

Task 1: Add Sessions to a Conference

Sessions were implemented using a conference ancestor relationship, allowing for querying via the ancestor key.  As ancestors cannot be changed, the design decision is that sessions belong to a conference and will not move to another. In addition, one conference can have many sessions.

Contrast that to the speaker implementation, it is the has-a relationship, a property of the session in this case.  This can cause issues with naming consistency and therefore query and memcache issues based on name not picked from a list (potentially) - but this could be addressed in the front end of the application.

The following are session properties:

name - string, required
highlights - string, repeated
speaker - string, required
duration - string
typeOfSession - string
date - date (yyyy-mm-dd)
startTime - time (hh:mm (24 hour format))
organizerUserid - string

The date and startTime properties are converted into datetime objects using the datetime.strptime() method.  This allows for consistency when querying the data (such as sessions that start before 7 p.m).

As you can see speaker is a string property to allow for simple queries.  A drawback to this no consistency in adding speaker names (last, first, capitalization, etc.)  Another approach would be to register speakers to allow for a speaker entity (similar to user profiles).

I added the following endpoints in support of Sessions:

1. getConferenceSessions(websafeConferenceKey) -- Given a conference, return all sessions
2. getConferenceSessionsByType(websafeConferenceKey, typeOfSession) Given a conference, return all sessions of a specified type (eg lecture, keynote, workshop)
3. getSessionsBySpeaker(speaker) -- Given a speaker, return all sessions given by this particular speaker, across all conferences
4. createSession(SessionForm, websafeConferenceKey) -- open only to the organizer of the conference

Task 2: Add Sessions to User Wishlist

Two endpoints were created for handling user wishlists:

1. addSessionToWishlist(SessionKey) -- adds the session to the user's list of sessions they are interested in attending

2. getSessionsInWishlist() -- query for all the sessions that the user is interested in

Task 3: Work on indexes and queries

Session indexes were added to index.yaml.

Two new endpoints were created for administrative functionality:

1. getIncompleteConferences - list the conferences that have default values for city, maxAttendees or topic

2. getInCompleteSessions - list the session that have default values for speaker, duration or type

Solve the following query related problem

Let’s say that you don't like workshops and you don't like sessions after 7 pm. How would you handle a query for all non-workshop sessions before 7 pm? What is the problem for implementing this query? What ways to solve it did you think of?

I created the before7NonWorkshopQuery endpoint which stores the query results of non-workshop sessions in a list, then iterates the list and returnss the sessions that start before 7 p.m. as we cannot have an ndb query that has  inequalities of 2 different properties.

Task 4: Add a Task

A new task set_featured_speaker is called when a session is created.  This task checks to see if the speaker is listed in more than one session, and if so, adds the speaker and the sessions to memcache.

The getFeaturedSpeaker endpoint was created to retrieve the featured speakers from memcache.



Setup Instructions

1. Update the value of `application` in `app.yaml` to the app ID you
   have registered in the App Engine admin console and would like to use to host
   your instance of this sample.
2. Update the values at the top of `settings.py` to
   reflect the respective client IDs you have registered in the
   [Developer Console][4].
3. Update the value of CLIENT_ID in `static/js/app.js` to the Web client ID
4. (Optional) Mark the configuration files as unchanged as follows:
   `$ git update-index --assume-unchanged app.yaml settings.py static/js/app.js`
5. Run the app with the devserver using `dev_appserver.py DIR`, and ensure it's running by visiting your local server's address (by default [localhost:8080][5].)
6. (Optional) Generate your client library(ies) with [the endpoints tool][6].
7. Deploy your application.


[1]: https://developers.google.com/appengine
[2]: http://python.org
[3]: https://developers.google.com/appengine/docs/python/endpoints/
[4]: https://console.developers.google.com/
[5]: https://localhost:8080/
[6]: https://developers.google.com/appengine/docs/python/endpoints/endpoints_tool

