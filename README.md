# Combining Drat and Travis
[![Build Status](https://travis-ci.org/csgillespie/dratTravis.png?branch=master)](https://travis-ci.org/csgillespie/dratTravis)

 * [Drat](https://github.com/eddelbuettel/drat) is a useful little package, for, *inter-alia*, using
github *gh-pages* as an R repository
 * [Travis](https://github.com/craigcitro/r-travis) is a hosted continuous integration service.
 It is integrated with gitHub and offers first class support for a number of languages, but importantly, R.

This package illustrates how to automatically update a `drat` github repository, on a *successful* travis build.

## Getting Started

1. Set up a [drat](https://github.com/eddelbuettel/drat) repo

1. Add [travis](https://github.com/craigcitro/r-travis) support to your package

1. Get a github [API token](https://github.com/settings/tokens/new) to generate
a new Personal access token. You might need to re-enter your password. Just select `repo`, or
if your repository is public, you can select `public_repo` instead. GitHub will create the token and
give you a chance to copy it down.

1. Travis will need to [encrypt](http://docs.travis-ci.com/user/encryption-keys/) this token. The short story is:
  ```
  # Install Ruby first. Will probably require sudo
  gem install travis
  ```
  If `sudo` isn't available, you can do a local install
  ```
  # Look at ~/.gem/
  gem install --user-install travis
  ```
1. `cd` to the R package directory, and run
  ```
  travis encrypt GH_TOKEN=$MY_ACCESS_TOKEN --add env.global
  ```
  where `$MY_ACCESS_TOKEN` is your access token. The `--add env.global` automatically adds the token to your `.travis.yml` file
1. Copy across `deploy.sh` and edit appropriately.
1. Add the line
  ```
  after_success:
    - test $TRAVIS_REPO_SLUG == "csgillespie/dratTravis" && test $TRAVIS_PULL_REQUEST == "false" && test $TRAVIS_BRANCH == "master" && bash deploy.sh
  ```

    to your `.travis.yml` file. This means your `drat` repository is only updated:
    - for successful builds
    - if the commit is on the master you own, i.e. USERNAME/dratTRAVIS
    - not for pull requests
    - only on the master branch

## TODO

1. The bash script is a bit hacky.
1. It should be possible to only update if a version is tagged. Travis provides a variable `$TRAVIS_TAG`, that is empty if the commit isn't a tag. **But** when tagging, `$TRAVIS_BRANCH` also changes to the tag name.
