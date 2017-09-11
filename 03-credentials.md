# Chapter 3: Credentials

Allowing users to log into your app with Facebook, Google, or other external authentication services requires the right credentials.

## Facebook
* If you don't already have a Facebook account, create one.
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
