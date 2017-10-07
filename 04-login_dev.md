# Chapter 4: OmniAuth Login (Development Environment)

In this chapter, you will provide OmniAuth login capabilities in the development environment.  The OmniAuth logins will NOT work in the production environment yet.  (You will provide this capability later.)

## Credentials
At this point, trying to log in through the browser on your local version of the app gives you errors even though the tests pass.  You need to create your app on each of the OmniAuth services you wish to provide, get the right credentials, and then provide them in environment variables.

### Facebook
* Go to the [Facebook for Developers page](https://developers.facebook.com/).
* Create a new app.  Fill in the name of your app with the same name you've been using in GitHub and Heroku.
* Go to the Dashboard to see the App ID and App Secret.  For easier reference, save your App ID and App Secret in KeePassX (or your preferred password manager).
* From your app's Dashboard, click on "Add Product".  Select "Facebook Login" and press "Set Up".  Select "Web".
* Click on "Settings".
* Add the following URLs under Valid OAuth redirect URIs:
```
http://localhost:3000/users/auth/facebook/callback
http://localhost:3001/users/auth/facebook/callback
http://localhost:3002/users/auth/facebook/callback
http://localhost:3003/users/auth/facebook/callback
http://localhost:3004/users/auth/facebook/callback
http://localhost:3005/users/auth/facebook/callback
http://localhost:3006/users/auth/facebook/callback
http://localhost:3007/users/auth/facebook/callback
http://localhost:3008/users/auth/facebook/callback
http://localhost:3009/users/auth/facebook/callback
http://localhost:3010/users/auth/facebook/callback
```
* Click on "Save Changes" to save your settings.

### GitHub
* From your browser, log into your GitHub account.  Go to the upper right corner of the page and click on the menu button.  Select "Settings".
* On the left side of your settings page, go down to the "Developer Settings" section and click on "OAuth Apps".
* Click on "Register a New Application".  Fill in the name of your app with the same name you've been using in GitHub and Heroku.  Fill in the URL of your app on Heroku for the Homepage URL.  For the callback URL, use "http://localhost:3000/users/auth/github/callback".  (NOTE: If the host machine port that maps to port 3000 in Docker, then use that port instead of 3000 in this URL.)
* Get the client ID and secret ID.  For easier reference, save these values in KeePassX (or your preferred password manager).

### Google
* Go to the [Google Developers Console](https://console.developers.google.com).
* Click on "Create Credentials", and select "OAuth client ID".  Select "Web application".  Fill in the name of your app with the same name you've been using in GitHub and Heroku.
* In the list of URLs under "Authorized JavaScript origins", add the following URLs:
```
http://localhost:3000
http://localhost:3001
http://localhost:3002
http://localhost:3003
http://localhost:3004
http://localhost:3005
http://localhost:3006
http://localhost:3007
http://localhost:3008
http://localhost:3009
http://localhost:3010
```
* In the list of URLs under "Authorized redirect URIs", add the following URLs:
```
http://localhost:3000/users/auth/google_oauth2/callback
http://localhost:3001/users/auth/google_oauth2/callback
http://localhost:3002/users/auth/google_oauth2/callback
http://localhost:3003/users/auth/google_oauth2/callback
http://localhost:3004/users/auth/google_oauth2/callback
http://localhost:3005/users/auth/google_oauth2/callback
http://localhost:3006/users/auth/google_oauth2/callback
http://localhost:3007/users/auth/google_oauth2/callback
http://localhost:3008/users/auth/google_oauth2/callback
http://localhost:3009/users/auth/google_oauth2/callback
http://localhost:3010/users/auth/google_oauth2/callback
```
* Press the "Create" button.
* Get the client ID and client secret.  For easier reference, save these values in KeePassX (or your preferred password manager).

## Configuring the Development Environment
* In your command line terminal, enter the command "sh config_env.sh".  When prompted, enter the appropriate OmniAuth credential.
* Restart the local Rails server.
* You should now be able to log into the local version of your app with the OmniAuth services through your browser.
* This still is not enough to log into your app with the OmniAuth services in Heroku.


