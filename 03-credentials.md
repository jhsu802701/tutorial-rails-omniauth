# Chapter 3: Credentials

Allowing users to log into your app with Facebook, Google, or other external authentication services requires the right credentials.  If you don't already have an account with each of these services, be sure to create one first.

## Facebook
* Go to the [Facebook for Developers page](https://developers.facebook.com/).
* Create a new app.  Fill in the name of your app with the same name you've been using in GitHub and Heroku.
* Go to the Dashboard to see the App ID and App Secret.
* For easier reference, save your App ID and App Secret in KeePassX (or your preferred password manager).
* From your app's Dashboard, add the Facebook Login feature.  In the list of "Valid OAuth redirect URIs", add the following URLs:
```
http://localhost:3000/
http://localhost:3001/
http://localhost:3002/
http://localhost:3003/
http://localhost:3004/
http://localhost:3005/
http://localhost:3006/
http://localhost:3007/
http://localhost:3008/
http://localhost:3009/
http://localhost:3010/
```
* In addition, add the URL of your app in the production environment.

## Google
* Go to the [Google Developers Console](https://console.developers.google.com).
* On the left side of the screen, click on "Credentials", and then create a new project.  Fill in the name of your app with the same name you've been using in GitHub and Heroku.
* Click on "Create Credentials", and select "OAuth client ID".  Fill in the OAuth consent screen.
* After you submit your OAuth consent form, you'll be asked for the application type.  Select "web application", and fill in the appropriate authorized redirect URL.
* You will now see your client ID and client secret.
* For easier reference, save your App ID and App Secret in KeePassX (or your preferred password manager).
