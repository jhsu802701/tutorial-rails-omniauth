# Ruby on Racetracks Tutorial: Omniauth

Welcome to the [Ruby on Racetracks](http://www.rubyonracetracks.com/) Tutorial on OmniAuth!

## What's the Point?
In this tutorial, you will add the Omniauth feature to a Rails app.  This allows users (and admins) to log in through Google or Facebook.

## Prerequisites
* Docker should be installed.  This is covered in the [Docker Tutorial](https://github.com/jhsu802701/tutorial-docker-stretch).
* You should have one of my rbenv-general Docker images installed.  This is covered in the [Docker Tutorial](https://github.com/jhsu802701/tutorial-docker-stretch).
* You should be familiar with the process of creating a new Rails app.  This is covered in the [GenericApp Tutorial](https://gist.github.com/jhsu802701/ace85adf7c3f197391c4457dec863e89).

## Notes
* GitHub authentication is not included, because the option of providing multiple callback URLs (which Facebook and Google offer) is not available.
* Twitter authentication is not included.  My attempts to provide Twitter authentication were thwarted by a cookie overflow resulting from an oversized cookie.
