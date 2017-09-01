# Chapter 2: Updating the User Model

In this chapter, you will update the user model.

## Procedure
* Add the provider and uid parameters to the user model by entering the following command:
```
rails g migration AddOmniauthToUsers provider:index uid:index
```
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.  If all goes well, you are ready to move on.

## Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Updated the user model to prepare for OmniAuth"
git push origin master
```
