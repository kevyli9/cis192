Collaborators: none  
Extra credit: none  

Note: I could not get my Poetry package to install correctly (I went to office hours), so I just ran pip freeze > requirements.txt  

Routes  
* '/' (splash view): 
    * for logged-in users, displays: text entry box to write a tweet, recent tweets, a list of all hashtags, and an option to log out
    * for not logged-in users: displays site title with description, and a link to sign in or create an account
* 'login/' (login view): provides an option to sign up and an option to login (for existing users)
* 'logout/' (logout view): logs current user out and redirects to the login view
* 'signup/' (signup view): creates an account for the user and logs them in
* 'delete' (delete view): delete a selected tweet (provided the user wrote that tweet)
* 'hashtag' (hashtag view): displays all tweets with the given hashtag
* 'like/' (like view): increments/decrements the number of likes for a given tweet
* 'profile/' (profile view): displays all tweets written by a given user  

Design considerations  
* Models:  
    * Tweet: 3 fields (body of the tweet, time-stamp, and author) to represent a tweet. 'author' field is made a ForeignKey to a User object (many-to-one) enabling multiple tweets to be associated with a given user.  
    * Liker: 2 fields (user id, tweet object). 'user_id' is used to indicate the logged-in user liking a tweet. 'tweet' is made a ForeignKey to a Tweet object (many-to-one) enabling multiple likes to be associated with a given tweet.  
    * Hashtag: 3 fields (tag, associated tweets, and time-stamp). 'tag' is the tag itself. 'tweets' are made a many-to-many relationship to Tweet, enabling multiple tags to be associated with a given tweet and multiple tweets to be associated with a given tag.  
* Views: one view is created for each route (indicated above). Tweets and hashtags are always ordered in terms of recency.
    * splash: different templates are rendered depending on if the user is authenticated (home.html) or not (splash.html). Parses for hashtags in the body of tweets posted by users; splits the body by spaces and looks for tokens that start with '#'. Creates a new Hashtag object only if the hashtag doesn't already exist.  
    * login_view: different templates are rendered depending on if the user is authenticated (home.html) or not (accounts.html). As such, if a user doesn't enter a username and password that match an existing account, they will have to try to log-in again or create a new account.  
    * logout_view: logs current user our and redirects to the login view  
    * signup_view: logs the user in if they succesfully create a new account and renders the home page such that they can immediately begin using the site.  
    * delete_view: deletes the tweet only if the session user is the tweet's author   
    * hashtag_view: retrieves all Tweet objects with the given hashtag, then renders the hashtag page
    * like_view: in other views, the number of likes for a given tweet is calculated by counting the number of Likers associated with the Tweet object. As such, like_view checks if the current user is in the liker_set: it will delete the Liker object if it already exists (to unlike the tweet) and will create a new Liker object if it does not exist (to like the tweet).  
    * profile_view: retrieves all Tweet objects associated with a given user, then renders the profile page  
  
Running the server: after unzipping the submission zip file, cd to the top directory ('twitter'), then run: python manage.py migrate  
Next, run: python manage.py runserver  
Then go to localhost:8000 in your browser.